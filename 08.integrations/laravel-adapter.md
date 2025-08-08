# Laravel HTTP Adapter

The Laravel HTTP Bridge provides the ability to convert internal requests and 
responses of the [Schema API](schemes-api.md) to those compatible with the 
[Laravel Framework](https://laravel.com).

<note>
This bridge is NOT included by default in the `boson-php/runtime` 
and must be installed separately.
</note>


## Installation

Via [Composer](https://getcomposer.org/doc/01-basic-usage.md#installing-dependencies):

```bash
composer require boson-php/laravel-http-bridge
```

**Requirements:**

* `PHP ^8.4`
* `illuminate/http ^12.0`

## Usage

<warning>
Correct functionality of Laravel is not guaranteed at the moment.
</warning>

To work with Laravel HTTP kernel you can use specific
`Boson\Bridge\Laravel\Http\LaravelHttpAdapter` adapter.

```php
use Boson\Application;
use Boson\ApplicationCreateInfo;
use Boson\Bridge\Laravel\Http\LaravelHttpAdapter;
use Boson\WebView\Api\Schemes\Event\SchemeRequestReceived;

// Create an application
$app = new Application(new ApplicationCreateInfo(
    schemes: [ 'laravel' ],
));

// Do not forget to fix for known Laravel issue with using
// shared kernel for web, cli and other environments:
// $_SERVER['APP_RUNNING_IN_CONSOLE'] = false;

// Create Laravel HTTP adapter
$laravel = new LaravelHttpAdapter();

// Subscribe to receive a request
$app->on(function (SchemeRequestReceived $e) use ($laravel): void {
    $laravelRequest = $laravel->createRequest($e->request);
    
    // ...do something, like:
    // 
    // $app = require __DIR__ . '/bootstrap/app.php';
    //
    // Container::setInstance($app);
    // Facade::clearResolvedInstances();
    // Facade::setFacadeApplication($app);
    //
    // $kernel = $app->make(HttpKernelContract::class);
    // $laravelResponse = $kernel->handle($laravelRequest);
    //
    
    $e->response = $laravel->createResponse($laravelResponse);
    
    //
    // $kernel->terminate($symfonyRequest, $symfonyResponse);
    // $app->terminate();
    //
});

$app->webview->url = 'laravel://app/example';
```

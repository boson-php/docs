# Laravel HTTP Adapter

You can create a Laravel HTTP request in the same way as Symfony.

<warning>
Correct functionality is NOT GUARANTEED.

It is not possible to make this framework work quickly and stably
due to architectural issues, a lot of memleaks, side effect and
other "bad practice features".
</warning>

To work with Laravel HTTP kernel you can use specific
`Boson\Bridge\Http\LaravelHttpAdapter` adapter.

```php
use Boson\Application;
use Boson\ApplicationCreateInfo;
use Boson\Bridge\Http\LaravelHttpAdapter;
use Boson\WebView\Event\WebViewRequest;

// Create an application
$app = new Application(new ApplicationCreateInfo(
    schemes: ['laravel'],
));

// Do not forget to fix for known Laravel issue with using
// shared kernel for web, cli and other environments:
// $_SERVER['APP_RUNNING_IN_CONSOLE'] = false;

// Create Laravel HTTP adapter
$laravel = new LaravelHttpAdapter();

// Subscribe to receive a request
$app->on(function (WebViewRequest $e) use ($laravel): void {
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

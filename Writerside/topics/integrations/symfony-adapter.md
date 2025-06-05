# Symfony HTTP Adapter

The Symfony HTTP Bridge provides the ability to convert internal requests and
responses of the [Schema API](schemes-api.md) to those compatible with the
[Symfony Framework](https://symfony.com).

The bridge is supplied as a separate component and must be installed separately.

## Installation

<tldr>
    <p>
        Via <a href="https://getcomposer.org/doc/01-basic-usage.md#installing-dependencies">Composer</a>:
    </p>
    <p>
        <code lang="bash">composer require boson-php/symfony-http-bridge</code>
    </p>
</tldr>

**Requirements:**

* `PHP ^8.4`
* `symfony/http-foundation ^6.4|^7.0`

## Usage

To work with Symfony HTTP kernel you can use specific 
`Boson\Bridge\Symfony\Http\SymfonyHttpAdapter` adapter.

```php
use Boson\Application;
use Boson\ApplicationCreateInfo;
use Boson\Bridge\Symfony\Http\SymfonyHttpAdapter;
use Boson\WebView\Api\Schemes\Event\SchemeRequestReceived;

// Create an application
$app = new Application(new ApplicationCreateInfo(
    schemes: [ 'symfony' ],
));

// Create Symfony HTTP adapter
$symfony = new SymfonyHttpAdapter();

// Subscribe to receive a request
$app->on(function (SchemeRequestReceived $e) use ($symfony): void {
    $symfonyRequest = $symfony->createRequest($e->request);
    
    // ...do something, like:
    //
    // $kernel->boot();
    // $symfonyResponse = $kernel->handle($symfonyRequest);
    //
    
    $e->response = $symfony->createResponse($symfonyResponse);
    
    // if ($kernel instanceof TerminableInterface) {
    //     $kernel->terminate($symfonyRequest, $symfonyResponse);
    // }
});

$app->webview->url = 'symfony://app/example';
```

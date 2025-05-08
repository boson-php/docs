# Symfony HTTP Adapter

To work with Symfony HTTP kernel you can use specific 
`Boson\Bridge\Http\SymfonyHttpAdapter` adapter.

```php
use Boson\Application;
use Boson\ApplicationCreateInfo;
use Boson\Bridge\Http\SymfonyHttpAdapter;
use Boson\WebView\Event\WebViewRequest;

// Create an application
$app = new Application(new ApplicationCreateInfo(
    schemes: ['symfony'],
));

// Create Symfony HTTP adapter
$symfony = new SymfonyHttpAdapter();

// Subscribe to receive a request
$app->on(function (WebViewRequest $e) use ($symfony): void {
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

# PSR-7 HTTP Adapter

To work with any PSR-7/17-compatible framework 
(e.g. [Yii3](https://github.com/yiisoft/demo),
[Spiral](https://spiral.dev/), [Slim](https://www.slimframework.com/), etc.),
you are provided with a corresponding adapter `Boson\Bridge\Http\Psr7HttpAdapter`.

```php
use Boson\Application;
use Boson\ApplicationCreateInfo;
use Boson\Bridge\Http\Psr7HttpAdapter;
use Boson\WebView\Event\WebViewRequest;

// Create an application
$app = new Application(new ApplicationCreateInfo(
    schemes: ['psr7'],
));

// Create PSR-7 HTTP adapter
$psr7 = new Psr7HttpAdapter(
    requests: new YourVendorPsr17ServerRequestFactory(),
);

// Subscribe to receive a request
$app->on(function (WebViewRequest $e) use ($psr7): void {
    $psr7Request = $psr7->createRequest($e->request);
    
    // ...do something, like:
    // 
    // $psr7Response = $app->handle($psr7Request);
    //
    
    $e->response = $psr7->createResponse($psr7Response);
});

$app->webview->url = 'psr7://app/example';
```

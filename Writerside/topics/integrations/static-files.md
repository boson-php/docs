# Static Files

Static adapters exist to serve files without execution of application code.

## Filesystem

To return static files from the filesystem, you can use specific 
`Boson\Bridge\Static\FilesystemStaticAdapter` static adapter.

```php
use Boson\Application;
use Boson\ApplicationCreateInfo;
use Boson\Bridge\Static\FilesystemStaticAdapter;
use Boson\WebView\Event\WebViewRequest;

// Create an application
$app = new Application(new ApplicationCreateInfo(
    schemes: ['static'],
));

// Create static files adapter
$static = new FilesystemStaticAdapter([__DIR__ . '/public']);

$app->on(function (WebViewRequest $e) use ($static): void {
    // Lookup static file and create response in
    // case of given file is available.
    $e->response = $static->lookup($e->request);
    
    if ($e->response !== null) {
        return;
    }
    
    // Do something else...
});

$app->webview->url = 'static://localhost/example/image.png';
```

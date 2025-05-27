# Static Files

Static provider exist to serve files without execution of application code.

The provider is supplied as a separate component and must be installed separately.

## Installation

<tldr>
    <p>
        Via <a href="https://getcomposer.org/doc/01-basic-usage.md#installing-dependencies">Composer</a>:
    </p>
    <p>
        <code lang="bash">composer require boson-php/http-static-provider</code>
    </p>
</tldr>

**Requirements:**

* `PHP ^8.4`
* `psr/http-message ^1.0|^2.0`
* `psr/http-factory ^1.0`

## Usage

Static adapters exist to serve files without execution of application code.

## Filesystem

To return static files from the filesystem, you can use specific 
`Boson\Component\Http\Static\FilesystemStaticAdapter` static adapter.

```php
use Boson\Application;
use Boson\ApplicationCreateInfo;
use Boson\Bridge\Static\FilesystemStaticAdapter;
use Boson\WebView\Api\Schemes\Event\SchemeRequestReceived;

// Create an application
$app = new Application(new ApplicationCreateInfo(
    schemes: ['static'],
));

// Create static files adapter
$static = new FilesystemStaticAdapter([__DIR__ . '/public']);

$app->on(function (SchemeRequestReceived $e) use ($static): void {
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

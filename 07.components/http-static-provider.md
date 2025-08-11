# HTTP Static Provider 

Static provider exist to serve files without execution of application code.

> This component is not included by default in the `boson-php/runtime`
> and must be installed separately.
{.warning}


## Installation

Via [Composer](https://getcomposer.org/doc/01-basic-usage.md#installing-dependencies):

```bash
composer require boson-php/http-static-provider
```

**Requirements:**

* `PHP ^8.4`


## Filesystem

To return static files from the filesystem, you can use specific 
`Boson\Component\Http\Static\FilesystemStaticProvider` static adapter.

```php
use Boson\Application;
use Boson\ApplicationCreateInfo;
use Boson\Component\Http\Static\FilesystemStaticProvider;
use Boson\WebView\Api\Schemes\Event\SchemeRequestReceived;

// Create an application
$app = new Application(new ApplicationCreateInfo(
    schemes: ['static'],
));

// Create static files adapter
$static = new FilesystemStaticProvider([__DIR__ . '/public']);

$app->on(function (SchemeRequestReceived $e) use ($static): void {
    // Lookup static file and create response in
    // case of given file is available.
    $e->response = $static->findFileByRequest($e->request);
    
    if ($e->response !== null) {
        return;
    }
    
    // Do something else...
});

$app->webview->url = 'static://localhost/example/image.png';
```

> Please note that the file search is performed by the path from the URL, 
> excluding the host, scheme, etc. Thus, the file that will be requested at 
> the address `scheme://HOST/path/to/file.png` must be located in
> `/public/path/to/file.png`.
{.warning}

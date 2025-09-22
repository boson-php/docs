# Extensions

Each webview contains a list of extensions that enhance the webview's
functionality. A list of all available extensions is configured in the
`WebViewCreateInfo::$extensions` field:

```php
new Boson\WebView\WebViewCreateInfo(
    extensions: [
        ...Boson\WebView\WebViewCreateInfo::DEFAULT_WEBVIEW_EXTENSIONS,
        // ...
        new ExampleCustomExtension(),
        new ExampleAnotherExtension(),
    ],
);
```

In addition, you can use the `WebViewCreateInfo::extensions()` method for 
more convenient filtering and adding extensions:

```php
new Boson\WebView\WebViewCreateInfo(
    extensions: Boson\WebView\WebViewCreateInfo::extensions(
        with: [ new ExampleCustomExtension() ],
        except: [ ExampleDisabledExtension::class ],
    ),
),
```

You can completely disable all available extensions by specifying an empty list:

```php
new Boson\WebView\WebViewCreateInfo(extensions: []);
```

Boson application includes a list of both built-in extensions (defined in
`Boson\WebView\WebViewCreateInfo::DEFAULT_WEBVIEW_EXTENSIONS` constant) and
external ones that can be installed separately using `composer install <ext-name>`.

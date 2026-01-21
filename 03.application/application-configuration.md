# Configuration

The application configuration class `Boson\ApplicationCreateInfo` is
<span term="optional class">optional</span> and serves as a convenient
way to define default settings for initializing your app.


## Application Name

The name of the application.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    name: 'Example Application',
);
```

The value is optional and can be used for user needs, as well as for internal ones.

> For example, as a [WindowClass (WNDCLASS.lpszClassName)](https://learn.microsoft.com/en-us/windows/win32/learnwin32/creating-a-window#window-classes)
> identifier on Windows OS.


## Intercepted Schemes

Defines custom schemes that your application can handle.
These schemes allow you to create custom protocols for your application.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    schemes: [ 'boson' ], // Default is empty array
);
```

More detailed information about the schemes is available
on the [Schemes API](../05.webview/schemes-api.md) page.

> Each registered scheme in this list will produce a
> [SchemeRequestReceived](../05.webview/schemes-api.md#request-intention) intention
> when attempting to access a resource located at an address with this protocol.


## Debug Mode

Enables or disables debug features, like dev tools and logging. When enabled,
provides additional diagnostic information and developer tools.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    debug: true, // Default is null
);
```

> If the value is not specified, the debug mode will be set according to the
> current `php.ini` settings (depends on whether you are using the
> development `php.ini` settings)
{.note}

> The debug mode settings also affect the default settings of child
> configurations, such as [developer tools](../05.webview/webview-configuration.md#dev-tools)
> (if they are not set explicitly).


## Runtime Library

Specifies the path to a custom
[frontend library](https://github.com/boson-php/frontend-src/releases) that should
be loaded with the application.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    library: __DIR__ . '/path/to/custom/library.dll', // Default is null
);
```

> In most cases this is not required, and the library will be selected
> automatically based on the current operating system and CPU architecture.
{.note}


## Extensions

You can explicitly specify a list of application extensions by passing an 
`array` (`iterable`) in the `extensions` field.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    extension: [
        new ExampleApplicationExtension(),
    ],
);
```

> You can read more about extensions in the [application extensions](../03.application/application-extensions.md) section.
> {.note}

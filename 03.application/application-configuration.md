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

> For example as a [WindowClass (WNDCLASS.lpszClassName)](https://learn.microsoft.com/en-us/windows/win32/learnwin32/creating-a-window#window-classes)
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


## Threads Count

Specifies the number of physical threads for the application. This affects how
many concurrent operations your application can handle.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    threads: 4, // Default is null
);
```

> If the value is not specified (defined as `null`), the number of
> threads will correspond to the number of cores in the CPU.
{.note}


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

> The debug mode settings also affects the default settings of child
> configurations, such as [developer tools](../05.webview/webview-configuration.md#dev-tools)
> (if they are not set explicitly).


## Application Library

Specifies the path to a custom
[frontend library](https://github.com/boson-php/frontend-src/releases) that should
be loaded with the application.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    library: __DIR__ . '/path/to/custom/library.dll', // Default is null
);
```

> In most cases this is not required and the library will be selected
> automatically based on the current operating system and CPU architecture.
{.note}


## Quit On Close

Determines whether the application should terminate when all windows are closed.
If set to `false`, the application will continue running in the background.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    quitOnClose: true, // Default is true
);
```


## Autorun

Responsible for automatic application launch. If autorun is set to
`false`, you will need to launch the application yourself at the
moment when it is needed.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    autorun: false, // Default is true
);
```

> Autorun will NOT work if the application has already been launched manually.
> ```php
> $app = new Boson\Application();
> 
> // After calling "run()" method, autorun is disabled
> $app->run();
> ```
{.warning}

> Autorun will NOT work if the application has been stopped manually.
> ```php
> $app = new Boson\Application();
> 
> // After calling "quit()" method, autorun is disabled
> $app->quit();
> ```
{.warning}

> Autorun will NOT work if any serious errors (errors or exceptions)
> occurred before launching.
> ```php
> $app = new Boson\Application();
> 
> // If errors occur before launch, autorun is disabled
> trigger_error('smth happen');
> ```
{.warning}

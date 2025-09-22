# Extensions

Each application contains a list of extensions that enhance the app's 
functionality. A list of all available extensions is configured in the
`ApplicationCreateInfo::$extensions` field:

```php
new Boson\ApplicationCreateInfo(
    extensions: [
        ...Boson\ApplicationCreateInfo::DEFAULT_APPLICATION_EXTENSIONS,
        // ...
        new ExampleCustomExtension(),
        new ExampleAnotherExtension(),
    ],
);
```

In addition, you can use the `ApplicationCreateInfo::extensions()` method for 
more convenient filtering and adding extensions:

```php
new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        with: [ new ExampleCustomExtension() ],
        except: [ ExampleDisabledExtension::class ],
    ),
),
```

You can completely disable all available extensions by specifying an empty list:

```php
new Boson\ApplicationCreateInfo(extensions: []);
```

Boson application includes a list of both built-in extensions (defined in
`Boson\ApplicationCreateInfo::DEFAULT_APPLICATION_EXTENSIONS` constant) and
external ones that can be installed separately using `composer install <ext-name>`.



## Quit On Close

Determines whether the application should terminate when all windows are closed.

> This extension does not provide public API

To disable the extension, add class `Boson\Api\QuitOnClose\QuitOnCloseExtension`
to the exclusions list.

```php
use Boson\Api\QuitOnClose\QuitOnCloseExtension;

new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        except: [ QuitOnCloseExtension::class ],
    ),
);
```



## Autorun

Responsible for automatic deferred application launch.

> This extension does not provide public API

To disable the extension, add class `Boson\Api\Autorun\AutorunExtension`
to the exclusions list.

```php
use Boson\Api\Autorun\AutorunExtension;

new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        except: [ AutorunExtension::class ],
    ),
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



## Console

Disables the console/terminal (if present) after building the application
in non-debug mode.

If the extension is disabled, the console will not be disabled (detached) after
the application is launched, and all output (errors and messages) will be 
visible to the user.

> This extension does not provide public API

To disable the extension, add class `Boson\Api\Console\ConsoleExtension`
to the exclusions list.

```php
use Boson\Api\Console\ConsoleExtension;

new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        except: [ ConsoleExtension::class ],
    ),
);
```



## Quit Signals

Graceful terminates the application when receiving shutdown messages such as:
- `SIGINT` (Interrupt Signal) on *nix OS
- `SIGQUIT` (Quit Signal) on *nix OS
- `SIGTERM` (Terminate Signal) on *nix OS
- <kbd>Ctrl + C</kbd> key combinations (if a terminal is available) on Windows OS

To disable the extension, add class `Boson\Api\QuitSignals\QuitSignalsExtension`
to the exclusions list.

```php
use Boson\Api\QuitSignals\QuitSignalsExtension;

new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        except: [ QuitSignalsExtension::class ],
    ),
);
```



## CPU Info

Provides information about the CPU for which the application was initialized.

> This extension provides a public API using the `Application::$cpu` property.

> More information about the public API is available on the 
> [relevant documentation page](../03.application/cpu-api.md).
{.note}

To disable the extension, add class `Boson\Api\CentralProcessor\CentralProcessorExtension`
to the exclusions list.

```php
use Boson\Api\CentralProcessor\CentralProcessorExtension;

new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        except: [ CentralProcessorExtension::class ],
    ),
);
```



## OS Info

Provides information about the Operating System for which the application was initialized.

> This extension provides a public API using the `Application::$os` property.

> More information about the public API is available on the
> [relevant documentation page](../03.application/os-api.md).
{.note}

To disable the extension, add class `Boson\Api\OperatingSystem\OperatingSystemExtension`
to the exclusions list.

```php
use Boson\Api\OperatingSystem\OperatingSystemExtension;

new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        except: [ OperatingSystemExtension::class ],
    ),
);
```



## System Dialogs

The API provides functionality for interacting with files and
directories through dialog windows.

> This extension provides a public API using the `Application::$dialog` property.

> More information about the public API is available on the
> [relevant documentation page](../03.application/dialog-api.md).
{.note}

To disable the extension, add class `Boson\Api\Dialog\DialogExtension`
to the exclusions list.

```php
use Boson\Api\Dialog\DialogExtension;

new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        except: [ DialogExtension::class ],
    ),
);
```
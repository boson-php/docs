# Extensions

Each application contains a list of extensions that enhance the app's 
functionality. A list of all available extensions is configured in the
`ApplicationCreateInfo::$extensions` field:

```php
new Boson\ApplicationCreateInfo(
    extensions: [
        ...Boson\ApplicationCreateInfo::DEFAULT_APPLICATION_EXTENSIONS,
        // ...
        new ExampleCustomExtensionProvider(),
        new ExampleAnotherExtensionProvider(),
    ],
);
```

In addition, you can use the `ApplicationCreateInfo::extensions()` method for 
more convenient filtering and adding extensions:

```php
new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        with: [ new ExampleCustomExtensionProvider() ],
        except: [ ExampleDisabledExtensionProvider::class ],
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

To disable the extension, add class `Boson\Api\QuitOnClose\QuitOnCloseExtensionProvider`
to the exclusions list.

```php
use Boson\Extension\ExtensionProviderInterface;
use Boson\Api\QuitOnClose\QuitOnCloseExtensionProvider;

new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        except: [ QuitOnCloseExtensionProvider::class ],
    ),
);
```


## Autorun

Responsible for automatic deferred application launch.

> This extension does not provide public API

To disable the extension, add class `Boson\Api\Autorun\AutorunExtensionProvider`
to the exclusions list.

```php
use Boson\Extension\ExtensionProviderInterface;
use Boson\Api\Autorun\AutorunExtensionProvider;

new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        except: [ AutorunExtensionProvider::class ],
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

To disable the extension, add class `Boson\Api\Console\ConsoleExtensionProvider`
to the exclusions list.

```php
use Boson\Extension\ExtensionProviderInterface;
use Boson\Api\Console\ConsoleExtensionProvider;

new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        except: [ ConsoleExtensionProvider::class ],
    ),
);
```


## Quit Handler

Graceful terminates the application when receiving shutdown messages such as:
- [Linux/macOS] – `SIGINT` (Interrupt Signal)
- [Linux/macOS] – `SIGQUIT` (Quit Signal)
- [Linux/macOS] – `SIGTERM` (Terminate Signal)
- [Windows] – <kbd>Ctrl + C</kbd> key combinations (if a terminal is available)

To disable the extension, add class `Boson\Api\QuitHandler\QuitHandlerExtensionProvider`
to the exclusions list.

```php
use Boson\Extension\ExtensionProviderInterface;
use Boson\Api\QuitHandler\QuitHandlerExtensionProvider;

new Boson\ApplicationCreateInfo(
    extensions: Boson\ApplicationCreateInfo::extensions(
        except: [ QuitHandlerExtensionProvider::class ],
    ),
);
```
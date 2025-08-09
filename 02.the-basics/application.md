# Application

The `Boson\Application` is the central 
component of the Boson and is responsible for managing the application 
lifecycle. It provides a single entry point for creating and managing web 
applications using WebView.

The application is responsible for:

- Lifecycle management ([startup](application.md#creating),
  [shutdown](application.md#stopping), etc).
- [Window](window.md) creation and management.
- [WebView](webview.md) integration for web content display.
- [Application](application-events.md), [WebView](webview-events.md) and 
  [Window](webview-events.md) event handling.

...and more

Architecturally, the application, like most key components, is divided into 
two "layers":
- The main functionality belonging to the application itself.
- Facades over methods and properties of descendants for quick access to the 
  main internal components of the core.

## Creating

To create an application, simply create a new `Boson\Application` object. 
This will be sufficient for the vast majority of cases.

```php
$app = new Boson\Application();
```

The application constructor also contains several optional arguments that you 
can pass explicitly if you wish.

The first optional argument is responsible for the `Boson\ApplicationCreateInfo` 
application settings and allows you to fine-tune the application's operation.

> More details about the application configuration are written on the 
> [corresponding documentation pages](application.md#configuration).


```php
$config = new Boson\ApplicationCreateInfo(
    // application configuration options
);

$app = new Boson\Application(info: $config);
```

The remaining optional parameters are responsible for passing external 
dependencies. 

For example, the second argument takes an optional reference to an external 
`Psr\EventDispatcher\EventDispatcherInterface` event dispatcher 
to which all events within the application can be delegated.

```php
$dispatcher = new Any\Vendor\PsrEventDispatcher();

$app = new Boson\Application(dispatcher: $dispatcher);
```

After creating the application, you will have access to the API to work with 
it, and after the necessary actions, the application will automatically start, 
<a href="application-configuration.md#autorun">unless otherwise specified</a>.

## Launching

The application can be started manually using the `run()` method. 

```php
$app = new Boson\Application();
$app->run();
```

> The `run()` method is **blocking**, which means it will block 
> the current execution thread until the application is stopped.
> ```php
> $app = new Boson\Application();
> 
> echo 'Application will start...';
> 
> $app->run(); // This is a blocking operation
> 
> echo 'Application WAS stopped'; // The code will be executed ONLY 
>                                 // after stopping an application
> ```
{.warning}


## Stopping

The application can be stopped at any time using the `quit()` method:

```php
$app->quit();
```

> For correct organization of the code, the stop should be made from the 
> event subscription
> ```php
> $app = new Boson\Application();
> 
> $app->on(function (SomeEvent $e) use ($app): void {
>     $app->quit();
> });
> 
> $app->run();
> ```

To find out if the application is running, you can use the 
`Application::$isRunning` property, which returns `true` if the application 
is currently running.

```php
$app = new Boson\Application();

// any code

if ($app->isRunning === false) {
    $app->run();
}
```


## Identifier

The `Boson\ApplicationId` is a unique identifier for each application
instance. The identifier is needed to compare different applications
for their equivalence.

To get application identifier use the `Application::$id` property.

```php
$app = new Boson\Application();

echo 'ID: ' . $app->id;
```

An identifier is a value object and contains methods
for comparison and conversion to scalars.

```php
if ($app1->id->equals($app2->id)) {
    echo sprintf('The %s app is equals to %s app', $app1, $app2);
}
```


## Configuration

The application configuration class `Boson\ApplicationCreateInfo` is
<span term="optional class">optional</span> and serves as a convenient
way to define default settings for initializing your app.


### Application Name

The name of the application.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    name: 'Example Application',
);
```

The value is optional and can be used for user needs, as well as for internal ones.

> For example as a [WindowClass (WNDCLASS.lpszClassName)](https://learn.microsoft.com/en-us/windows/win32/learnwin32/creating-a-window#window-classes)
> identifier on Windows OS.


### Intercepted Schemes

Defines custom schemes that your application can handle.
These schemes allow you to create custom protocols for your application.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    schemes: [ 'boson' ], // Default is empty array
);
```

More detailed information about the schemes is available
on the [Schemes API](schemes-api.md) page.


> Each registered scheme in this list will produce a
> [SchemeRequestReceived](schemes-api-events.md#request-intention) intention
> when attempting to access a resource located at an address with this protocol.


### Threads Count

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


### Debug Mode

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
> configurations, such as [developer tools](webview.md#dev-tools) 
> (if they are not set explicitly).



### Application Library

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


### Quit On Close

Determines whether the application should terminate when all windows are closed.
If set to `false`, the application will continue running in the background.

```php
$appConfig = new Boson\ApplicationCreateInfo( 
    quitOnClose: true, // Default is true
);
```


### Autorun

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

## Events

The application will automatically emit the following events (and intentions)
during its lifecycle.

To subscribe to events, you can use direct access to the
<a href="events.md#event-listener">event listener</a>.

```php
$app->addEventListener(Event::class, function (Event $e) {
    var_dump($e);
});
```

The application instance also supports a more convenient and simple way of
registering events using the `on()` method.

```php
$app->on(function (Event $event): void {
    var_dump($event);
});
```

> More information about events can be found in the 
> [events documentation](events.md).
{.note}


### Starting Intention

An `Boson\Event\ApplicationStarting` intention to start the application.

```php
class ApplicationStarting<Application>
```

> If it is cancelled, the application will not be launched.


### Started Event

An `Boson\Event\ApplicationStarted` event fired after the application has been
launched and the `Boson\Event\ApplicationStarting` intention has not been
cancelled.

```php
class ApplicationStarted<Application>
```

### Stopping Intention

An `Boson\Event\ApplicationStopping` intention to stop the application.

```php
class ApplicationStopping<Application>
```

> If it is cancelled, the application will not be stopped.


### Stopped Event

An `Boson\Event\ApplicationStopped` event fired after the application has been
stopped and the `Boson\Event\ApplicationStopping` intention has not been
cancelled.

```php
class ApplicationStopped<Application>
```

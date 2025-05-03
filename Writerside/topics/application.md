# Application

The `Application` is the central component of the Boson and is responsible for 
managing the application lifecycle. It provides a single entry point for 
creating and managing web applications using WebView.

The application is responsible for:

- Lifecycle management (startup, shutdown)
- Window creation and management
- WebView integration for web content display
- Application, webview and window event handling

...and more

Architecturally, the application, like most key components, is divided into 
two "layers":
- The main functionality belonging to the application itself.
- Facades over methods and properties of descendants for quick access to the 
  main internal components of the core.


## Creating

To create an application, simply create a new <code>Boson\Application</code> 
object. This will be sufficient for the vast majority of cases.

<code-block lang="PHP">
$app = new Boson\Application();
</code-block>

The application constructor also contains several optional arguments that you 
can pass explicitly if you wish.

The first optional argument is responsible for the <code>Boson\ApplicationCreateInfo</code> 
application settings and allows you to fine-tune the application's operation.

<tip>
More details about the application configuration are written on the 
<a href="configuration.md#application">corresponding documentation pages</a>.
</tip>

<code-block lang="PHP">
$config = new Boson\ApplicationCreateInfo(
    // application configuration options
);

$app = new Boson\Application(info: $config);
</code-block>

The remaining optional parameters are responsible for passing external 
dependencies. 

For example, the second argument takes an optional reference to an external 
<code>Psr\EventDispatcher\EventDispatcherInterface</code> event dispatcher 
to which all events within the application can be delegated.

<code-block lang="PHP">
$dispatcher = new Any\Vendor\PsrEventDispatcher();

$app = new Boson\Application(dispatcher: $dispatcher);
</code-block>

After creating the application, you will have access to the API to work with 
it, and after the necessary actions, the application will automatically start, 
<a href="configuration.md#autorun">unless otherwise specified</a>.

## Running and Stopping

The application can be started manually using the <code>run()</code> method. 

<code-block lang="PHP">
$app = new Boson\Application();
$app->run();
</code-block>

<warning>
The <code>run()</code> method is <b>blocking</b>, which means it will block 
the current execution thread until the application is stopped.

<code-block lang="PHP">
$app = new Boson\Application();

echo 'Application will start...';

$app->run(); // This is a blocking operation

echo 'Application WAS stopped'; // The code will be executed ONLY 
                                // after stopping an application
</code-block>
</warning>

The application can be stopped at any time using the `quit()` method:

<code-block lang="PHP">
$app->quit();
</code-block>

<tip>
For correct organization of the code, the stop should be made from the 
event subscription
<code-block lang="PHP">
$app = new Boson\Application();

$app->events->addEventListener(SomeEvent::class, 
    function() use ($app): void {
        $app->quit();
    }
);

$app->run();
</code-block>
</tip>

To find out if the application is running, you can use the 
`Application::$isRunning` property, which returns `true` if the application 
is currently running.

<code-block>
$app = new Boson\Application();

// any code

if ($app->isRunning === false) {
    $app->run();
}
</code-block>

## Main Window

<tooltip term="TODO">TODO</tooltip>

## Main WebView

<tooltip term="TODO">TODO</tooltip>

## Application Events

The application will automatically emit the following events (and intentions)
during its lifecycle:

- <code>ApplicationStarting</code> - when the application is about to start. 
  <tip>This is an <b>intention</b>: You can prevent it from running using the 
  <code>$starting->cancel()</code> method.</tip>

- <code>ApplicationStarted</code> - when the application has started.
  <tip>This is an <b>event</b>: Tells you that the application has already started. 
  You cannot influence this in any way.</tip>

- <code>ApplicationStopping</code> - when the application is about to stop.
  <tip>This is an <b>intention</b>: You can prevent it from stopping using the 
  <code>$stopping->cancel()</code> method.</tip>

- <code>ApplicationStopped</code> - when the application has stopped.
  <tip>This is an <b>event</b>: Tells you that the application has already stopped. 
  You cannot influence this in any way.</tip>

More information about events can be found in the
<tooltip term="TODO">events documentation</tooltip>.

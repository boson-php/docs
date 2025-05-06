# Application

<show-structure for="chapter" depth="2"/>

The `Application` is the central component of the Boson and is responsible for 
managing the application lifecycle. It provides a single entry point for 
creating and managing web applications using WebView.

The application is responsible for:

- Lifecycle management (
  <a href="application.md#creating-an-application">startup</a>, 
  <a href="application.md#stop-application">shutdown</a>)
- <a href="window.md">Window</a> creation and management
- <a href="webview.md">WebView</a> integration for web content display
- <a href="application.md#application-events">Application</a>, webview and window event handling

...and more

Architecturally, the application, like most key components, is divided into 
two "layers":
- The main functionality belonging to the application itself.
- Facades over methods and properties of descendants for quick access to the 
  main internal components of the core.


## Creating an Application

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

## Launching an Application

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


## Stop Application

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

<code-block lang="PHP">
$app = new Boson\Application();

// any code

if ($app->isRunning === false) {
    $app->run();
}
</code-block>


## Application Identifier

The <code>Boson\ApplicationId</code> is a unique identifier for each application
instance. The identifier is needed to compare different applications
for their equivalence.

<warning>
The <code>ApplicationId</code> property is read-only and cannot be changed.
</warning>

The <code>ApplicationId</code> identifier is a value object and contains methods
for comparison and conversion to scalars.

<code-block lang="PHP">
if ($app1->id->equals($app2->id)) {
    echo sprintf('The %s app is equals to %s app', $app1, $app2);
}
</code-block>


## Application Events
<primary-label ref="events"/>

The application will automatically emit the following events (and intentions)
during its lifecycle.

<note>
More information about events can be found in the <a href="events.md">events 
documentation</a>.
</note>

### Starting Intention
<secondary-label ref="intention"/>

An `Boson\Event\ApplicationStarting` intention to start the application. 

<tip>
If it is cancelled, the application will not be launched.
</tip>

### Started Event
<secondary-label ref="event"/>

An `Boson\Event\ApplicationStarted` event fired after the application has been 
launched and the `Boson\Event\ApplicationStarting` intention has not been 
cancelled.

### Stopping Intention
<secondary-label ref="intention"/>

An `Boson\Event\ApplicationStopping` intention to stop the application. 

<tip>
If it is cancelled, the application will not be stopped.
</tip>

### Stopped Event
<secondary-label ref="event"/>

An `Boson\Event\ApplicationStopped` event fired after the application has been
stopped and the `Boson\Event\ApplicationStopping` intention has not been
cancelled.
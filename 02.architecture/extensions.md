# Extensions

Extensions are a set of functionality that extends the capabilities of the core a
nd can provide a public API for accessing its capabilities.

Each system part, such as an [Application](../03.application/application-extensions.md), 
[Window](../04.window/window-extensions.md), or [WebView](../05.webview/webview-extensions.md), 
contains its own set of extensions.

## Configuration

Extensions are registered in configuration rules, so the
configuration might look like this:

```php
$config = new Boson\ApplicationCreateInfo(
    extensions: [ /* list of Application extensions */ ],
    
    window: new Boson\Window\WindowCreateInfo(
        extensions: [ /* list of Window extensions */ ],

        webview: new Boson\WebView\WebViewCreateInfo(
            extensions: [ /* list of WebView extensions */ ],
        ),
    ),
);
```

By default, if you do not specify `extensions` field explicitly, the list of 
extensions defined in the constants of the corresponding configuration 
DTO will be loaded.

```php
use Boson\ApplicationCreateInfo;
use Boson\Window\WindowCreateInfo;
use Boson\WebView\WebViewCreateInfo;

$config = new ApplicationCreateInfo(
    // by default
    extensions: ApplicationCreateInfo::DEFAULT_APPLICATION_EXTENSIONS,
    
    window: new WindowCreateInfo(
        // by default
        extensions: WindowCreateInfo::DEFAULT_WINDOW_EXTENSIONS, 

        webview: new WebViewCreateInfo(
            // by default
            extensions: WebViewCreateInfo::DEFAULT_WEBVIEW_EXTENSIONS,
        ),
    ),
);
```

For ease of extension management, each configuration DTO has an `extensions()` 
method available, allowing you to add and filter the list of extensions.

The `extensions()` method contains two arguments `with` and `except`, where:
- `with` – A list of extension instances that should be added to the default 
  set of extensions.
- `except` – A list of extension classes to disable/exclude.

```php
$config = new Boson\ApplicationCreateInfo(
    // by default
    extensions: Boson\ApplicationCreateInfo::extensions(
        with: [ /* list of Application extension instances */ ],
        except: [ /* list of excluded Application extension classes */ ],
    ),
    
    window: new Boson\Window\WindowCreateInfo(
        extensions: /* etc... */
    ),
);
```


## Writing Extensions

All extensions extend the `Boson\Extension\Extension` class. The extension 
class must contain a `load()` method that takes the current context and an 
event listener and must load the extension.

```php
use Boson\Application;
use Boson\Contracts\Id\IdentifiableInterface;
use Boson\Dispatcher\EventListener;
use Boson\Extension\Extension;

/**
 * @template-extends Extension<Application>
 */
final class ExampleExtension extends Extension
{
    public function load(IdentifiableInterface $ctx, EventListener $listener): ?object
    {
        // load an extension
    }
}
```

> Please note that it is recommended to explicitly specify the extension
> context using `@template-extends Extension<CONTEXT>`.
> 
> The context can be an `Boson\Application`, a `Boson\Window\Window`, or
> a `Boson\WebView\WebView`.
{.note}

To configure an extension, the ideal solution would be to create a 
configuration DTO that should be passed to the constructor.

```php
final readonly class ExampleExtensionCreateInfo
{
    public function __construct(
        public string $someOption = '',
        public bool $isAnotherOptionEnabled = true,
    ) {}
}

final class ExampleExtension extends Extension
{
    public function __construct(
        private readonly ExampleExtensionCreateInfo $info
            = new ExampleExtensionCreateInfo(),
    ) {}

    // ...
```

> It is advisable to specify all default values for each configuration DTO's field
{.note}


### Context Attachment

You may have noticed that the extensions `load()` method must return `?object`.
Each object that is returned from the `load()` method will be automatically
attached to the context and will be kept in memory as long as the context is
alive.

```php
final class ExampleExtension extends Extension
{
    public function load(IdentifiableInterface $ctx, EventListener $listener): ExampleLoadedContext
    {
        return new ExampleLoadedContext();
    }
}
```


### Public API

The loaded context can be explicitly declared as public (meaning it's accessible 
externally to the user). To do this, you should specify an extension "alias" 
using `#[AvailableAs]` attribute.

```php
use Boson\Extension\Attribute\AvailableAs;
use Boson\Extension\Extension;

#[AvailableAs('public_alias')]
#[AvailableAs(ExampleLoadedContext::class)]
final class ExampleExtension extends Extension
{
    // ...
```

You may have noticed that there are two types of aliases:
- `public_alias` – An alias for context's property:
    ```php
    // In case of Application extension
    $app->public_alias;

    // In case of Window extension
    $window->public_alias;

    // In case of WebView extension
    $webview->public_alias;
    ```
- `ExampleLoadedContext::class` – For access via container's service location:
    ```php
    // In case of Application extension
    $app->get(ExampleLoadedContext::class);

    // In case of Window extension
    $window->get(ExampleLoadedContext::class);

    // In case of WebView extension
    $webview->get(ExampleLoadedContext::class);
    ```


### Dependencies

In case you need a dependency on some other extension, you can specify it
using the `#[DependsOn]` attribute.

```php
use Boson\Extension\Attribute\DependsOn;
use Boson\Extension\Extension;

#[DependsOn(SomeExtension::class)]
#[DependsOn(AnotherExtension::class)]
final class ExampleExtension extends Extension
{
    public function load(IdentifiableInterface $ctx, EventListener $listener): ?object
    {
        $some = $ctx->get(SomeExtensionReference::class);
        $another = $ctx->get(AnotherExtensionReference::class);

```

By specifying a dependency, you **ensure** that the extension will be
loaded in the correct order.
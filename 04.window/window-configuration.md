# Configuration

The window configuration class `Boson\Window\WindowCreateInfo` is
<span term="optional class">optional</span> and serves as a convenient way to
define default settings for main window of your app.


## Title

Sets the title that appears in the window's title bar and taskbar.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    title: 'My Application Window',
);
```

> More information about the window title can be found in
> the [window documentation](../04.window/window.md#title).
{.note}


## Window Size (Width and Height)

Defines the initial dimensions of the window in pixels.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    width: 800,
    height: 600,
);
```

> More information about window size can be found in
> the [window documentation](../04.window/window.md#size).
{.note}


## Hardware Acceleration

Enables or disables hardware-accelerated rendering for all webview instances.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    enableHardwareAcceleration: true, // Default is true
);
```

**macOS/WebKit**

> Does not allow controlling hardware-acceleration.
>
> This configuration option has no effect.
{.warning}


## Dark Mode

Force dark mode for the window and all webviews in that window 
if the option is set to `true`.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    forceDarkMode: true, // Default is false
);
```


## Window Visibility

Controls whether the window is initially visible when created.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    visible: true, // Default is true
);
```

> More information about window visibility can be found in
> the [window documentation](../04.window/window.md#visibility).
{.note}


## Window Resizability

Determines if the window can be resized by the user.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    resizable: true, // Default is true
);
```


## Always On Top

Controls whether a window should stay on top of other windows.

```php
$windowConfig = new Boson\Window\WindowCreateInfo(
    alwaysOnTop: true, // Default is false
);
```

**Linux/GTK4**

> There is no way to artificially set window always on top.
>
> This configuration option has no effect.
{.warning}

> More information about window "always on top" feature can be found in
> the [window documentation](../04.window/window.md#always-on-top).
{.note}


## Click Through

Enables or disables the window's handling of mouse events.

```php
$windowConfig = new Boson\Window\WindowCreateInfo(
    clickThrough: true, // Default is false
);
```

> More information about window "click-through" feature can be found in
> the [window documentation](../04.window/window.md#click-through).
{.note}


## Window Decorations

Specifies the window's border, title bar style and other.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    decoration: Boson\Window\WindowDecoration::DEFAULT,
);
```

> More information about window decorations can be found in
> the [window documentation](../04.window/window.md#decorations).
{.note}


## Extensions

You can explicitly specify a list of window extensions by passing an
`array` (`iterable`) in the `extensions` field.

```php
$appConfig = new Boson\Window\WindowCreateInfo( 
    extension: [
        new ExampleWindowExtension(),
    ],
);
```

> You can read more about extensions in the [window extensions](../04.window/window-extensions.md) section.
> {.note}

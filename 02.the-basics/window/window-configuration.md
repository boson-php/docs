
# Window Configuration

<primary-label ref="configuration"/>
<show-structure for="chapter" depth="2"/>

The window configuration class `Boson\Window\WindowCreateInfo` is
<tooltip term="optional class">optional</tooltip> and serves as a convenient way to 
define default settings for main window of your app.

## Title

Sets the title that appears in the window's title bar and taskbar.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    title: 'My Application Window',
);
```

> More information about window title can be found in 
> the <a href="window.md#title">window documentation</a>.
{.note}


## Hardware Acceleration

Enables or disables hardware-accelerated rendering for better performance.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    enableHardwareAcceleration: true, // Default is true
);
```

<tabs>
<tab title="MacOS/WebKit">

> Does not allow to control hardware-acceleration.
> 
> This configuration option has no effect.
{.warning}

</tab>
</tabs>


## Window Size (Width and Height)

Defines the initial dimensions of the window in pixels.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    width: 800,
    height: 600,
);
```

> More information about window size can be found in 
> the <a href="window.md#size">window documentation</a>.
{.note}


## Window Resizability

Determines if the window can be resized by the user.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    resizable: true, // Default is true
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
> the <a href="window.md#visibility">window documentation</a>.
{.note}


## Always On Top

Controls whether a window should stay on top of other windows.

```php
$windowConfig = new Boson\Window\WindowCreateInfo(
    alwaysOnTop: true, // Default is false
);
```

<tabs>
<tab title="Linux/GTK4">

> There is no way to artificially set window always on top.
> 
> This configuration option has no effect.
{.warning}

</tab>
</tabs>

> More information about window "always on top" feature can be found in 
> the <a href="window.md#always-on-top">window documentation</a>.
{.note}


## Click Through

Enables or disables the window's handling of mouse events.

```php
$windowConfig = new Boson\Window\WindowCreateInfo(
    clickThrough: true, // Default is false
);
```

> More information about window "click-through" feature can be found in 
> the <a href="window.md#click-through">window documentation</a>.
{.note}


## Window Decorations

Specifies the window's border, title bar style and other.

```php
$windowConfig = new Boson\Window\WindowCreateInfo( 
    decoration: Boson\Window\WindowDecoration::Default,
);
```

> More information about window decorations can be found in 
> the <a href="window.md#decorations">window documentation</a>.
{.note}

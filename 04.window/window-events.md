# Events

The window will automatically emit the following events (and intentions)
during its lifecycle.

To subscribe to events, you can use direct access to the
[event listener](events.md#event-listener).

```php
$window = $app->window;

$window->addEventListener(Event::class, function (Event $e) {
    var_dump($e);
});
```

The window instance also supports a more convenient and simple way of
registering events using the `on()` method.

```php
$window->on(function (Event $event): void {
    var_dump($event);
});
```

> More information about events can be found in the
> [events documentation](../02.architecture/events.md).
{.note}


## Closing Intention

An `Boson\Window\Event\WindowClosing` intention to close the window.

> If it is cancelled, the window will not be closed.

```php
class WindowClosing<Window>
```


## Closed Event

An `Boson\Window\Event\WindowClosed` event fired after the window has been
closed and the `Boson\Window\Event\WindowClosing` intention has not been
cancelled.

```php
class WindowClosed<Window>
```


## Created Event

An `Boson\Window\Event\WindowCreated` event fired after window has been created.

```php
class WindowCreated<Window>
```


## Decorated Event

An `Boson\Window\Event\WindowDecorated` event fired after
[window controls](window.md#decorations) visibility changed.

```php
class WindowDecorated<Window> 
{
    public readonly bool $isDecorated;
}
```

- `$isDecorated` - Visibility status of the OS window controls.

> The event differs from a
> [decoration changed](window.md#decoration-changed-event) in
> that it reacts exclusively to the turning on or off of window controls
> (minimize, maximize, restore, close buttons and title bar) visibility.
{.note}


## Decoration Changed Event

An `Boson\Window\Event\WindowDecorationChanged` event fired after
[window decoration](window.md#decorations) has been changed.

```php
class WindowDecorationChanged<Window> 
{
    public readonly Boson\Window\WindowDecoration $decoration;
    public readonly Boson\Window\WindowDecoration $previous;
}
```

- `$decoration` - Decorations type of the window.
- `$previous` - Previous decorations type of the window.


## Destroyed Event

An `Boson\Window\Event\WindowDestroyed` event fired after window has
been destroyed (all references to it in the GC have been removed).

```php
class WindowDestroyed<Window>
```


## Focused Event

An `Boson\Window\Event\WindowFocused` event fired after
[window focus](window.md#focus) has been changed.

```php
class WindowFocused<Window> 
{
    public readonly bool $isFocused;
}
```

- `$isFocused` - Window [focus status](window.md#focus).

> The event is fired not only when window has been focused (in which case the
> `$isFocused` property will contain `true`), but also
> when window focus has been lost (in which case the `$isFocused`
> property will contain `false`).
{.note}


## Maximized Event

An `Boson\Window\Event\WindowMaximized` event fired after
[window maximized](window.md#maximize) state has been changed.

```php
class WindowMaximized<Window> 
{
    public readonly bool $isMaximized;
}
```

- `$isMaximized` - Window [maximized status](window.md#maximize).

> The event is fired not only when maximizing (in which case the
> `$isMaximized` property will contain `true`), but also
> when restoring from maximization (in which case the `$isMaximized`
> property will contain `false`).
{.note}


## Minimized Event

An `Boson\Window\Event\WindowMinimized` event fired after
[window minimized](window.md#minimize) state has been changed.

```php
class WindowMinimized<Window> 
{
    public readonly bool $isMinimized;
}
```

- `$isMinimized` - Window [minimized status](window.md#minimize).

> The event is fired not only when minimizing (in which case the
> `$isMinimized` property will contain `true`), but also when
> restoring from minimization (in which case the `$isMinimized`
> property will contain `false`).
{.note}


## Resized Event

An `Boson\Window\Event\WindowResized` event fired after
[window size](window.md#size) has been changed.

```php
class WindowResized<Window> 
{
    public readonly int $width;
    public readonly int $height;
}
```

- `$width` - Window width dimension in pixels.
- `$height` - Window height dimension in pixels.

> Window width and height is a non-negative `int32` (an integer value
> between 0 and 2147483647).


## State Changed Event

An `Boson\Window\Event\WindowStateChanged` event fired after
[window state](window.md#state) has been changed.

```php
class WindowStateChanged<Window> 
{
    public readonly Boson\Window\WindowState $state;
    public readonly Boson\Window\WindowState $previous;
}
```

- `$state` - State type of the window.
- `$previous` - Previous state type of the window.

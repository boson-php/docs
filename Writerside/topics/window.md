# Window

The `Window` class represents a window in the Boson application. It provides 
a way to manage window, including their properties, events, state, and 
associated WebView.

<note>
    The <tooltip term="main window">main window</tooltip> already available by 
    default in any application.
</note>
<note>
    To access the <tooltip term="main window">main window</tooltip>, use the 
    <code>$app->window</code> property, which is described on the 
    <a href="application.md#main-window">application documentation page</a>.
</note>

## Window Identifier

The <code>Boson\Window\WindowId</code> is a unique identifier for each window 
in the application. The identifier is needed to compare different windows 
for their equivalence.

<warning>
The <code>WindowId</code> property is <b>read-only</b> and cannot be changed.
</warning>

```php
public readonly WindowId $id
```

The <code>WindowId</code> identifier is a value object and contains methods for 
comparison and conversion to scalars.

<code-block lang="PHP">
if ($window1->id->equals($window2->id)) {
    echo sprintf('The %s is equals to %s', $window1, $window2);
}
</code-block>

<tip>
The <code>WindowId</code> is automatically generated when a window is created 
and remains constant throughout the window's lifetime.
</tip>

The identifier consists of two parts:
- A unique integer value that identifies the window in the application.
- A pointer to the native window handle.

<warning>
Please do not use the second <code>WindowId</code> argument unless you are sure. 
It provides unsafe access to the window handle pointer, for working with 
low-level API.
</warning>

In addition to the ability to convert to a string (i.e. implementations of the 
<code>Stringable</code> interface), this identifier can also be converted to an 
64-bit (or 32-bit on 32-bit OS) signed integer, which represents the actual 
physical address of the window pointer.

<code-block lang="PHP">
echo $window->id->toInteger();
</code-block>

<tip>
Technically, this behaviour can be used to pass a window pointer to 
subprocesses and then restore the pointer from it, like:

<code-block lang="PHP">
// process-1 
// somehow pass the scalar addr to the process-2
$addr = $window->id->toInteger();

// process-2
// somehow get a scalar addr value
$handle = $ffi->cast('saucer_handle*', $addr);
</code-block>

However, please note that this may cause ownership issues and should 
be used with caution.
</tip>

## Window Title

Contains title of the specified window encoded as UTF-8. The window title can 
be in any language and even include emojis. All line breaks (<code>\n</code>) 
and similar characters will be removed.

<img src="window-title.png" alt="Window Title" />

<note>
The header is both readable and writable.
</note>

To get the window title, simply read this property. The title will contain the 
real value, including all invisible (like <code>\n</code>) characters.

<code-block lang="PHP">
$title = $window->title;

echo 'Window Title: ' . $title;
</code-block>

To update the window title, set a new value to the property.

<code-block lang="PHP">
$window->title = 'New Window Title!';
</code-block>

<warning>
Indirect modification of <code>Window::$title</code> is <b>not allowed</b>, 
which means that this property cannot be passed by reference.

<code-block lang="PHP">
$title = &$window->title; // ❌ not available
</code-block>
</warning>


## Window State

<tooltip term="TODO">Documentation in progress...</tooltip>


## Window Decorations

<tooltip term="TODO">Documentation in progress...</tooltip>


## Window Size

<tooltip term="TODO">Documentation in progress...</tooltip>


## Window Visibility

<tooltip term="TODO">Documentation in progress...</tooltip>


## Window Close

<tooltip term="TODO">Documentation in progress...</tooltip>


## Window Events

<tooltip term="TODO">Documentation in progress...</tooltip>
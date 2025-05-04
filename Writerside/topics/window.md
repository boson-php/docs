# Window

<show-structure for="chapter" depth="2"/>

The `Window` class represents a window in the Boson application. It provides 
a way to manage window, including their properties, events, state, and 
associated WebView.

## Main Window

The `Application::$window` property provides convenient access to the
<tooltip term="main window">main window</tooltip> of the application. 

<tip>This is a <tooltip term="facade">facade property</tooltip> that internally 
accesses the default window inside the window manager</tip>

<code-block lang="PHP">
$app = new Boson\Application();

// Access the main window
$window = $app->window;
</code-block>

<warning>
If the <tooltip term="main window">main window</tooltip> is closed, the next 
available window from window manager will become the 
<tooltip term="main window">main window</tooltip>.

If you try to access the `$window` property after the all windows has been
closed, a `NoDefaultWindowException` will be thrown.
</warning>

<note>
    The <tooltip term="main window">main window</tooltip> already available by 
    default in any application.
</note>

## Window Identifier

The <code>Boson\Window\WindowId</code> is a unique identifier for each window 
in the application. The identifier is needed to compare different windows 
for their equivalence.

<warning>
The <code>WindowId</code> property is <b>read-only</b> and cannot be changed.
</warning>

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

Each window has several states. The window can be minimized, maximized to full 
screen, or in a normal state.

To get the current state of a window, you can use the <code>Window::$state</code> 
property. 

<code-block lang="PHP">
echo 'Window State: ' . $window->state->name;
</code-block>

<warning>
The <code>Window::$state</code> property is <b>read-only</b> and cannot be 
changed. To change state, the appropriate methods are used, described below.
</warning>

This property will contain one of the possible values of 
<code>Boson\Window\WindowState</code> enum.

<code-block lang="PHP">
enum WindowState
{
    /**
     * Standard window state with custom (user defined) sizes.
     */
    case Normal;

    /**
     * Maximized (i.e. zoomed) window state.
     */
    case Maximized;

    /**
     * Minimized (iconified) window state.
     */
    case Minimized;
}
</code-block>

There are corresponding methods for changing states from code.

### Minimize

In order to minimize the window, you should use the appropriate 
<code>Window::minimize()</code> method.

<code-block lang="PHP">
// Minimize window to tray
$window->minimize();
</code-block>

When restoring a window from the tray using the operating system features 
(for example, <shortcut>Alt + Tab</shortcut>), the previous state will be 
restored.

<tabs>
<tab title="Normal">
    <list type="decimal">
        <li>Window state is <code>Normal</code>.</li>
        <li>Execute <code>$window->minimize()</code></li>
        <li>Restore using OS features (like <shortcut>Alt + Tab</shortcut>).</li>
        <li>Window state is again <code>Normal</code>.</li>
    </list>
</tab>
<tab title="Maximized">
    <list type="decimal">
        <li>Window state is <code>Maximized</code>.</li>
        <li>Execute <code>$window->minimize()</code></li>
        <li>Restore using OS features (like <shortcut>Alt + Tab</shortcut>).</li>
        <li>Window state is again <code>Maximized</code>.</li>
    </list>
</tab>
</tabs>

### Maximize

In order to maximize the window, you should use the appropriate
<code>Window::maximize()</code> method.

<code-block lang="PHP">
// Maximize window from tray or normal state
$window->maximize();
</code-block>

### Restore

In order to restore the window state (that is, switch to a <code>Normal</code> 
state), you should use the appropriate <code>Window::restore()</code> method.

<code-block lang="PHP">
// Restore window state
$window->restore();
</code-block>


## Window Visibility

The `Window::$isVisible` property controls the visibility state of the window.
It allows you to show or hide the window programmatically.

<note>
    It is also worth noting that the initial state of the window visibility
    depends on the <a href="configuration.md#window-visibility">window 
    settings</a>. By default, the window is shown.
</note>

To check if a window is currently visible:

<code-block lang="PHP">
if ($window->isVisible) {
    echo 'Window is visible';
} else {
    echo 'Window is hidden';
}
</code-block>

<warning>
The visibility state is independent of the window's <code>Minimized</code>
/<code>Maximized</code> state. A window can be visible while <code>Minimized</code>
or hidden while <code>Maximized</code>.

Hidden windows are not displayed in the tray and cannot be restored using the
OS functionality.
</warning>

### Show Window

To show the window you may use desired <code>Window::show()</code> method.

<code-block lang="PHP">
// Show the window
$window->show();
</code-block>

<tip>
You can also show the window through a <code>Window::$isVisible</code> property. 
To do this, simply set the <code>true</code>.

<code-block lang="PHP">
// Show the window
$window->isVisible = true;
</code-block>
</tip>

### Hide Window

To hide the window you may use desired <code>Window::hide()</code> method.

<code-block lang="PHP">
// Hide the window
$window->hide();
</code-block>

<tip>
You can also hide the window through a <code>Window::$isVisible</code> property. 
To do this, simply set the <code>false</code>.

<code-block lang="PHP">
// Hide the window
$window->isVisible = false;
</code-block>
</tip>


## Window Size

<tooltip term="TODO">Documentation in progress...</tooltip>

## Window Decorations

The <code>Window::$decoration</code> property allows you to control the
window's appearance and style.

<code-block lang="PHP">
// Get current decoration mode
echo $window->decoration->name;
</code-block>

It supports different decoration modes defined in the 
<code>Boson\Window\WindowDecoration</code> enum.

<code-block lang="PHP">
enum WindowDecoration
{
    /**
     * Default window style.
     */
    case Default;

    /**
     * Default window style with preferred dark mode.
     */
    case DarkMode;

    /**
     * A "frameless" windows is a window which hides the default
     * window buttons & handle assigned to it by the operating system.
     */
    case Frameless;

    /**
     * Enables "frameless" mode and makes the window completely transparent
     */
    case Transparent;
}
</code-block>

Let's say we load the content as 
<code>&lt;div style="background: #fff">Hello World!&lt;/div></code> in webview. 
So the result with different decorations will look like this.

<tabs>
    <tab title="Default">
        <img src="window-decorations-normal.png" alt="Default" />
    </tab>
    <tab title="DarkMode">
        <img src="window-decorations-dark-mode.png" alt="Dark Mode" />
    </tab>
    <tab title="Frameless">
        <img src="window-decorations-frameless.png" alt="Frameless" />
    </tab>
    <tab title="Transparent">
        <img src="window-decorations-transparent.png" alt="Transparent" />
    </tab>
</tabs>

### Default

The standard window style with system default appearance (title bar, close, 
minimise and maximise buttons).

<code-block lang="PHP">
$window->decoration = WindowDecoration::Default;
</code-block>

### Dark Mode

Default window style with dark theme preference.

<code-block lang="PHP">
$window->decoration = WindowDecoration::DarkMode;
</code-block>

### Frameless

A frameless window hides the default window decorations (title bar, buttons) 
provided by the operating system.

<code-block lang="PHP">
$window->decoration = WindowDecoration::Frameless;
</code-block>

<warning>
When using frameless (or transparent) windows, you need to implement your own 
window controls and drag regions using HTML attributes.
</warning>

#### Dragging

You may use `data-webview-drag` HTML attribute to make elements draggable.

<code-block lang="HTML">
<![CDATA[
    <header data-webview-drag>
        <span>Custom Title Bar</span>
    </header>
]]>
</code-block>

#### Resizing

You may use <code>data-webview-resize</code> HTML attribute to create resize 
handles. The resize direction should be specified as the attribute value.

Possible values:
- <code>t</code> - Top resize handle.
- <code>b</code> - Bottom resize handle.
- <code>l</code> - Left resize handle.
- <code>r</code> - Right resize handle.

These values can also be combined, for example <code>tl</code> to simulate the top-left 
corner resize behavior.

<code-block lang="HTML">
<![CDATA[
    <div data-webview-resize="t">Top resize handle</div>
    <div data-webview-resize="b">Bottom resize handle</div>
    <div data-webview-resize="l">Left resize handle</div>
    <div data-webview-resize="r">Right resize handle</div>
    <div data-webview-resize="tl">Top-left corner</div>
    <div data-webview-resize="tr">Top-right corner</div>
    <div data-webview-resize="bl">Bottom-left corner</div>
    <div data-webview-resize="br">Bottom-right corner</div>
]]>
</code-block>

#### Prevent Drag/Resize Events

You may use <code>data-webview-ignore</code> HTML attribute to prevent elements from 
triggering window behavior.

<code-block lang="HTML">
<![CDATA[
    <!-- header is draggable -->
    <header data-webview-drag>
        <span>Custom Title Bar</span>
    
        <!-- except close button -->
        <button data-webview-ignore>Close</button>
    </header>
]]>
</code-block>

### Transparent

Enables <a href="#frameless">frameless</a> mode and makes the window 
background transparent.

<code-block lang="PHP">
$window->decoration = WindowDecoration::Transparent;
</code-block>

<tip>
With transparent windows, you should use CSS to control the background color:
<code-block lang="HTML">
<![CDATA[
    <style>
    body {
        background: rgba(255, 255, 255, .8);
    }
    </style>
    <body>
        Content
    </body>
]]>
</code-block>
</tip>

## Window Close

The <code>Window::close()</code> method allows you to close and destroy a 
window and its associated resources. This operation is irreversible - once a 
window is closed, it cannot be reopened.

<code-block lang="PHP">
// Close the window
$window->close();
</code-block>

<warning>
Closing a window is a destructive operation. All resources associated with the 
window, including its <a href="webview.md">WebView</a>, will be freed. 

Any attempts to use the window (except <code>Window::$isClosed</code> property) 
after closing will result in undefined behavior.
</warning>

You can check if a window is closed using the `$isClosed` property:

<code-block lang="PHP">
if ($window->isClosed) {
    echo 'Window is already closed';
} else {
    $window->close();
}
</code-block>

## Window Events

<tooltip term="TODO">Documentation in progress...</tooltip>
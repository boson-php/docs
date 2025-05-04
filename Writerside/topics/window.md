# Window

<show-structure for="chapter" depth="2"/>

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

<list>
<li>
    <code>Normal</code> - The window is in a normal state. Such a window can be 
    manually resized or moved. The window normally takes up part of the screen.
    <img src="window-normal.png" alt="Normal State"/>
</li>
<li>
    <code>Maximized</code> - The window is in a maximized state. These windows 
    expand to fill the entire screen and occupy its entire area.
    <img src="window-maximized.png" alt="Maximized State"/>
</li>
<li>
    <code>Minimized</code> - The window is in a minimized state. Such windows 
    are minimized to the tray (panel with applications) and are not displayed 
    on the screen.
    <img src="window-minimized.png" alt="Minimized State"/>
</li>
</list>

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

It is also worth noting that the initial state of the window visibility 
depends on the window settings. By default, the window is shown.

<note>
More information about window visibility can be found in the
<a href="configuration.md#window-visibility">window settings documentation</a>.
</note>

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

<tooltip term="TODO">Documentation in progress...</tooltip>


## Window Close

<tooltip term="TODO">Documentation in progress...</tooltip>


## Window Events

<tooltip term="TODO">Documentation in progress...</tooltip>
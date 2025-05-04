# WebView

<show-structure for="chapter" depth="2"/>

The `WebView` class represents a webview in the Boson application. It provides 
a way to manage html content, including JavaScript scripts, styles, functions, 
and more.

## Main WebView

The `Application::$webview` property provides convenient access to the WebView
instance of the <tooltip term="main window">main window</tooltip>.

<tip>This is a <tooltip term="facade">facade property</tooltip> that internally 
accesses the webview of the default window inside the window manager</tip>

<code-block lang="PHP">
$app = new Boson\Application();

// Access the main WebView
$webview = $app->webview;
</code-block>

<warning>
Behavior is similar to the <tooltip term="main window">main window</tooltip>:

If you try to access the `$webview` property after the all windows has been
closed, a `NoDefaultWindowException` will be thrown.
</warning>

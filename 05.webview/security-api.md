# Security API

This API provides information about 
[security context](https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts) 
used to access [other APIs](https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts/features_restricted_to_secure_contexts).

> This is a **built-in** extension and does not require separate installation.
>
> To disable the extension, remove `Boson\WebView\Api\Security\SecurityExtension`
> from the list of extensions.
>
> More information about extensions can be found on the [relevant documentation
> page](../05.webview/webview-extensions.md).
{.note}

The API is available in the `WebView::$security` property.

```php
$app = new Boson\Application();

$app->webview->security; // Access to Security API
```

> Your context will most likely be **secure** if you don't use the 
> `data:` or `about:` protocol schemes
{.note}

## Current Context

To get the current security status you can use the read-only 
`$isSecureContext` property.

```php
$isSecure = $app->webview->security->isSecureContext;

echo 'Context is ' . ($isSecure ? 'secure' : 'insecure');
// 
// Expects: Context is secure
//
```
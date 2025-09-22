# Network API

This API provides information about 
[network](https://developer.mozilla.org/en-US/docs/Web/API/Network_Information_API).

> This extension is NOT included by default in the `boson-php/runtime`
> and must be installed separately.
{.note}

> MacOS/WebKit does not support this API. In the future, it
> may be implemented using native OS calls.
{.macos}


## Installation

Via [Composer](https://getcomposer.org/doc/01-basic-usage.md#installing-dependencies):

```bash
composer require boson-php/webview-ext-network
```

After that, add the extension to the config:

```php
new Boson\WebView\WebViewCreateInfo(
    extensions: [
        ...Boson\WebView\WebViewCreateInfo::DEFAULT_WEBVIEW_EXTENSIONS,
        // ...
        new Boson\WebView\Api\Network\NetworkExtensionProvider(),
    ],
);
```


## Usage

The API is available in the `WebView::$network` property.

```php
$app = new Boson\Application();

$app->webview->network; // Access to Network API
```

> Please note that this information is specific to WebView and does not reflect
> **real network information**. This means that if the network is blocked via
> devtools (or OS politics), all network information will show that there is 
> no internet connection:
> - ❌ All JS `fetch` or `XHR` requests will **NOT** work.
> - ❌ All files (images, fonts, styles, scripts, etc.) from the internet
>   (i.e. `http://...`/`https://...`) will **NOT** be loaded.
> - ✅ PHP network requests (`file_get_contents()`, `curl_xxx()`, etc.) **WILL**
>   continue to work.
{.warning}

## Downlink

You may get the effective or max bandwidth estimate in megabits per second, 
rounded to the nearest multiple of 25 kilobits per seconds.

This value is based on recently observed application layer throughput across 
recently active connections, excluding connections made to a private address 
space. In the absence of recent bandwidth measurement data, the attribute value
is determined by the properties of the underlying connection technology.

> Please note that the values are approximate and do not reflect 
> actual throughput.

To obtain the "effective" bandwidth, use the `downlink` property. The 
`downlinkMax` property provides the maximal bandwidth.

```php
echo 'Effective bandwidth is '
    . $app->webview->network->downlink 
    . "\n";
    
echo 'Maximal bandwidth is '
    . ($app->webview->network->downlinkMax ?? 'unknown') 
    . "\n";

//
// Expects:
//
//    Effective bandwidth is 1.35
//    Maximal bandwidth is unknown
//
```

As you may have noticed, the `downlinkMax` property may contain `null` if
this information is not available. It is recommended to use `downlink` instead.

## Effective Type

Effective type means the approximate speed (type) of network available
on the client.

To get information about the effective network type, you should refer to
the `effectiveType` property. The property value can take one of the 
possible options of enum `Boson\WebView\Api\Network\NetworkEffectiveType`:

- `NetworkEffectiveType::None` – Means no internet connection.
- `NetworkEffectiveType::VerySlow` – Means a very slow connection; between GPRS and slow 2g.
- `NetworkEffectiveType::Slow` – Means a slow connection; approximately corresponds to 2g connection.
- `NetworkEffectiveType::Medium` – Means a medium connection; approximately corresponds to 3g connection.
- `NetworkEffectiveType::Fast` – Means a medium connection; approximately corresponds to 4g/LTE connection.
- `NetworkEffectiveType::Other` – Means that this information is not available.

```php
$type = $app->webview->network->effectiveType;

if (! in_array($type, [NetworkEffectiveType::Fast, NetworkEffectiveType::Other])) {
    throw new LogicException('Application requires a more stable network connection');
}
```

> Don't forget to add `NetworkEffectiveType::Other` to checks. It will be
> returned when definition or API functionality is unavailable.
{.warning}

## RTT

RTT is an estimated effective round-trip time of the current connection,
rounded to the nearest multiple of 25 milliseconds.

This value is based on recently observed application-layer RTT measurements
across recently active connections. It excludes connections made to a private
address space. If no recent measurement data is available, the value is based
on the properties of the underlying connection technology.

```php
if ($app->webview->network->rtt > 500) {
    throw new LogicException('Network latency is too high');
}
```

## Saving Traffic

This option may indicate the presence of a user requirement to save traffic.
This option can be applied both by Operating System policies and set
explicitly by the user.

If the `savingTraffic` property contains `true`, then it is recommended to
sacrifice the quality of media files (if they are downloaded from the physical
network) or the responsiveness of the application and save network traffic.

```php
if ($app->webview->network->savingTraffic) {
    throw new LogicException('Image loading is unavailable, traffic saving is enabled');
}

return file_get_contents('https://example.com/some-img.jpg');
```

## Network Type

Network type means the physical device through which the connection
to the network is established.

The list of available devices is defined in the 
`Boson\WebView\Api\Network\NetworkType` enum and can be the following:

- `NetworkType::None` – There is no internet connection.
- `NetworkType::Bluetooth` – Network connection via Bluetooth.
- `NetworkType::Cellular` – Network connection via cellular.
- `NetworkType::Ethernet` – Network connection via IEEE 802.3 (Ethernet).
- `NetworkType::WiFi` – Network connection via IEEE 802.11 (Wi-Fi).
- `NetworkType::WiMax` – Network connection via IEEE 802.16 (WiMAX).
- `NetworkType::Other` – In the case that it is impossible to 
  obtain this information.

```php
$device = $app->webview->network->type;

if (! in_array($device, [NetworkType::Ethernet, NetworkType::Other])) {
    throw new LogicException('Recommended to use a cable connection');
}
```

> Don't forget to add `NetworkType::Other` to checks. It will be
> returned when definition or API functionality is unavailable.
{.warning}

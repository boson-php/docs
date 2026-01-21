# Battery API

| WebView2 (Windows) | WebKitGtk (Linux) | QT (Linux) | WebKit (macOS) |
|--------------------|-------------------|------------|----------------|
| ✔                  | ✔                 | ✔          | ✘              |

The Battery Status API, more often referred to as the [Battery 
API](https://developer.mozilla.org/en-US/docs/Web/API/Battery_Status_API), 
provides information about the system's battery charge level and lets you be 
notified by events that are sent when the battery level or charging status
change. This can be used to adjust your app's resource usage to reduce battery
drain when the battery is low, or to save changes before the battery runs out 
in order to prevent data loss.

> This extension is NOT included by default in the `boson-php/runtime`
> and must be installed separately.
{.note}


## Installation

Via [Composer](https://getcomposer.org/doc/01-basic-usage.md#installing-dependencies):

```bash
composer require boson-php/webview-ext-battery
```

After that, add the extension to the config:

```php
new Boson\WebView\WebViewCreateInfo(
    extensions: [
        ...Boson\WebView\WebViewCreateInfo::DEFAULT_WEBVIEW_EXTENSIONS,
        // ...
        new Boson\WebView\Api\Battery\BatteryExtension(),
    ],
);
```


## Usage

The API is available in the `WebView::$battery` property.

```php
$app = new Boson\Application();

$app->webview->battery; // Access to Battery API
```


## Battery Level

To get the current battery charge level you can use the read-only 
`$level` property. The battery level contain a float value 
between `0.0` and `1.0`.

```php
$level = $app->webview->battery->level;

echo 'Charge level is ' . (int) ($level * 100) . '%';

// 
// Expects: Charge level is 100%
// 
```

> For non-mobile devices the charge level is always `1.0`
{.note}


## Charging Status

To get the battery charging status you can use the read-only
`$isCharging` property.

```php
$isCharging = $app->webview->battery->isCharging;

echo 'The battery is ' . ($isCharging ? '' : 'not ') . 'charging now';
// 
// Expects: The battery is charging now
// 
```

> For non-mobile devices the charging status is always `true`
{.note}


## Charging Time

To get the time until the battery is fully charged, use the read-only
`$chargingTime` property. The charging time property will contain an 
integer value in seconds.

```php
$chargingTime = $app->webview->battery->chargingTime;

echo vsprintf('It takes another %d seconds to fully charge', [
    $chargingTime,
]);
```

> For non-mobile devices the charging time is always `0`
{.note}


## Discharging Time

To get the time until the battery is fully discharged, use the read-only
`$dischargingTime` property. The discharging time property will 
contain an integer value in seconds or `null` in case the 
discharge time is not available.

```php
$dischargingTime = $app->webview->battery->dischargingTime;

if ($dischargingTime === null) {
    echo 'Battery is missing';
    
    return;
}

echo vsprintf('It takes another %d seconds to fully discharge', [
    $dischargingTime,
]);
```

> For non-mobile devices the discharging time is always `null`
{.note}


## Events

The battery will automatically emit the following events (and intentions)
during its lifecycle.

> More information about events can be found in the 
> [events documentation](../02.architecture/events.md).
{.note}


### Charging State Changed Event

An `Boson\WebView\Api\Battery\Event\BatteryChargingStateChanged` event fired
after charging state has been changed.

```php
class BatteryChargingStateChanged<WebView>
{
    public readonly bool $isCharging;
}
```


### Charging Level Changed Event

An `Boson\WebView\Api\Battery\Event\BatteryLevelChanged` event fired
after charging level has been changed.

```php
class BatteryLevelChanged<WebView>
{
    public readonly float<0.0, 1.0> $level;
}
```


### Charging Time Changed Event

An `Boson\WebView\Api\Battery\Event\BatteryChargingTimeChanged` event fired
after charging time has been changed.

```php
class BatteryChargingTimeChanged<WebView>
{
    public readonly int<0, max> $chargingTime;
}
```


### Discharging Time Changed Event

An `Boson\WebView\Api\Battery\Event\BatteryDischargingTimeChanged` event fired
after discharging time has been changed.

```php
class BatteryDischargingTimeChanged<WebView>
{
    public readonly ?int<0, max> $dischargingTime;
}
```

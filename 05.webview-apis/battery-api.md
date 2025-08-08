# Battery API

This API provides information about 
[device battery](https://developer.mozilla.org/en-US/docs/Web/API/Battery_Status_API).

<warning>
MacOS/WebKit does not support this API. In the future, it 
may be implemented using native syscalls.
</warning>

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
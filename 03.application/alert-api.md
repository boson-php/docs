# Alert API

The API provides the ability to create alerts (message boxes) with
custom messages.

> This extension is NOT included by default in the `boson-php/runtime`
> and must be installed separately.
{.note}

| WebView2 (Windows) | WebKitGtk (Linux) | QT (Linux) | WebKit (macOS) |
|--------------------|-------------------|------------|----------------|
| ✔                  | ✘                 | ✘          | ✔              |

## Installation

Via [Composer](https://getcomposer.org/doc/01-basic-usage.md#installing-dependencies):

```bash
composer require boson-php/app-ext-alert
```

After that, add the extension to the config:

```php
new Boson\ApplicationCreateInfo(
    extensions: [
        ...Boson\ApplicationCreateInfo::DEFAULT_APPLICATION_EXTENSIONS,
        // ...
        new Boson\Api\Alert\AlertExtension(),
    ],
);
```

## Usage

You can access the subsystem using `Application::$alert` property.

```php
$app = new Boson\Application();

$app->alert; // Access to Alert API
```

## Creating

To create an alert, you should use the `create()` method, passing there a
configuration DTO `Boson\Api\Alert\AlertCreateInfo` with a title and a message.

```php
use Boson\Application;
use Boson\Api\Alert\AlertCreateInfo;

$app = new Application();

$app->alert->create(new AlertCreateInfo(
    title: 'Title',
    text: 'Message',
));
```

After the call, you will receive a corresponding message box.

On **Windows**:

![Windows](https://habrastorage.org/webt/ka/jf/ih/kajfihxiiobgv7q5yqyjihbi88s.png)

On **macOS**:

![macOS](https://habrastorage.org/webt/xx/ne/ug/xxneug8ik7kymbt74gedjtki9yq.png)

> The alert call is blocking and stops the application until the user makes 
> a choice (clicks on the button).
{.warning}

## Buttons

The alert invocation returns the clicked button as a result. By default, 
there is only one button, like `Boson\Api\Alert\AlertButton::Ok`.

```php
$button = $app->alert->create(new AlertCreateInfo( 
    // ...
));

if ($button === Boson\Api\Alert\AlertButton::Ok) {
    echo 'OK button clicked!';
}
```

> The execution result may return `null`. This indicates an unexpected or
> abnormal termination of the alert. In most cases, this should not happen.

### Cancellation

You can add a second "Cancel" button using the `cancel: true` configuration 
option.

```php
$button = $app->alert->create(new AlertCreateInfo(
    // ...
    cancel: true,
));

if ($button === Boson\Api\Alert\AlertButton::Ok) {
    echo 'OK button clicked!';
}

if ($button === Boson\Api\Alert\AlertButton::Cancel) {
    echo 'Cancel button clicked!';
}
```

## Icons

By default, no icon is used when displaying an alert (or the system icon is 
used). To specify a custom icon, use the `icon` configuration field.

The `icon` can contain one of the following possible values:
- `null` – Default value (No Icon)
- `Boson\Api\Alert\AlertIcon::Info` – Information system icon.
  ![Info](https://habrastorage.org/webt/oc/33/sr/oc33srnf0y_ozlmiorlkkadkn7o.png)

- `Boson\Api\Alert\AlertIcon::Warning` – Warning system icon
  ![Warning](https://habrastorage.org/webt/w4/jv/jn/w4jvjn6qwfxltf1ayplwiqv0n_0.png)

- `Boson\Api\Alert\AlertIcon::Error` – Error system icon
  ![Error](https://habrastorage.org/webt/dr/mj/pb/drmjpbh_ume5zprtufyc2_dgu1u.png)


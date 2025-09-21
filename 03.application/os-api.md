# OS Info API

> This is a built-in extension and does not require separate installation.
>
> To disable the extension, remove `Boson\Api\OperatingSystem\OperatingSystemExtensionProvider`
> from the list of extensions.
>
> More information about extensions can be found on the [relevant documentation
> page](../03.application/application-extensions.md).
{.note}

This API provides information about the Operating System for which the
application was initialized.

You can access the subsystem using `Application::$os` property.

```php
$app = new Boson\Application();

$app->os; // Access to OS Info API
```

## OS Family

To get information about the OS family, use the `family` property:

```php
$app = new Boson\Application();

echo $app->os->family;

//
// Expected output:
//
//   Windows
//
```

> More detailed information about OS families is available on the 
> [OS Info Component](../07.components/os-info.md#os-families) 
> documentation page.
{.tip}

## Common Info

To get information about the OS name and version, use the `name` and `version` 
properties.

You also have access to optional (may contain `null`) information about 
the OS edition and codename.

```php
$app = new Boson\Application();

echo $app->os->name . "\n";
echo $app->os->version . "\n";
echo ($app->os->edition ?? '~') . "\n";
echo ($app->os->codename ?? '~') . "\n";

//
// Expected output:
//
//   Windows 11 Pro
//   10.0.26100
//   Professional
//   24H2
//
```

> More detailed information about OS is available on the
> [OS Info Component](../07.components/os-info.md#basic-detection)
> documentation page.
{.tip}
# CPU Info API

This API provides information about the CPU for which the
application was initialized.

You can access the subsystem using `Application::$cpu` property.

```php
$app = new Boson\Application();

$app->cpu; // Access to CPU Info API
```

## CPU Architecture

To get information about the CPU architecture, use the `arch` property:

```php
$app = new Boson\Application();

echo $app->cpu->arch;

//
// Expected output:
//
//   amd64
//
```

> More detailed information about CPU architectures is available on the 
> [CPU Info Component](../07.components/cpu-info.md#cpu-architecture) 
> documentation page.
{.tip}

## Common Info

To get information about the CPU name and cores count, use the `name`,
`physicalCores` and `logicalCores` properties.

You also have access to optional (may contain `null`) information about 
the CPU vendor.

```php
$app = new Boson\Application();

echo $app->cpu->name . "\n";
echo $app->cpu->physicalCores . "\n";
echo $app->cpu->logicalCores . "\n";
echo ($app->cpu->vendor ?? '~') . "\n";

//
// Expected output:
//
//   AMD Ryzen 9 5900X 12-Core Processor
//   12
//   24
//   AuthenticAMD
//
```

> More detailed information about CPU is available on the
> [CPU Info Component](../07.components/cpu-info.md#basic-information)
> documentation page.
{.tip}
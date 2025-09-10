# Installation

Boson brings together a set of components, each handling a key part of the system:

- [**Runtime**](https://github.com/boson-php/runtime) — acts as the bridge
  between your PHP code and the underlying operating system. This is the main
  API that you can use both in runtime and during development.

- [**Compiler**](https://github.com/boson-php/compiler) – allows you to build
  the results of your work into a finished project ready for distribution.

> Technically there are more repositories and components, but the main ones
> are presented here.
{.note}

Together, they create a seamless environment for building rich desktop
applications with web technologies — without the bloat.

## Requirements

- Operating System:
  - Windows 10+ (x86 or AMD64)
  - MacOS 14+ (x86, AMD64 or ARM64)
  - Linux (x86, AMD64 or ARM64)
- PHP 8.4+
  > PHP is not required if you are building an executable 
  > from source. For development purposes only.
  - An `ext-ffi` extension

### Linux Dependencies

The Linux OS requires a few additional dependencies:
- gtk4 (>=4.12)
- webkitgtk-6.0

**Ubuntu**

```bash
apt install libgtk-4-1 libwebkitgtk-6.0-4
```

**Debian**

```bash
apt install libgtk-4-1 libwebkitgtk-6.0-4
```

> Debian 12 may include GTK 4.8. Make sure you have the correct 
> version (4.12+) installed using the command:
> ```bash
> dpkg -l | grep libgtk-4
> ```
> 
> The command will display the versions of installed packages.
> ```bash
> libgtk-4-1:amd64          4.14.5+ds-2deb12u1  amd64 ...
> libgtk-4-bin              4.14.5+ds-2deb12u1  amd64 ...
> libgtk-4-common           4.14.5+ds-2deb12u1  all   ...
> libgtk-4-dev:amd64        4.14.5+ds-2deb12u1  amd64 ...
> libgtk-4-media-gstreamer  4.14.5+ds-2deb12u1  amd64 ...
> ```
{.warning}

**Fedora**

```bash
dnf install gtk4 webkitgtk6.0
```

**FreeBSD**

```bash
pkg install webkit2-gtk4
```


## Runtime

Boson runtime provides the core of the library and allows you to
run your wonderful applications.

Library is available as [Composer](https://getcomposer.org/doc/) repository and
can be installed using the following command in a root of your project:

```shell
composer require boson-php/runtime
```

Don't forget to include the autoload file in your application.

```php
<?php

require __DIR__ . '/vendor/autoload.php';

$app = new Boson\Application();
```



## Compiler

Boson compiler gives you the ability to assemble the result of your work into
a final product. That is, into an executable file for the target platform.

Library is available as [Composer](https://getcomposer.org/doc/) repository and
can be installed using the following command in a root of your project:

```shell
composer require boson-php/compiler --dev
```

> The compiler is only required for development, it is not required for code
> execution, so it is recommended to include it as a `--dev` package.
{.note}


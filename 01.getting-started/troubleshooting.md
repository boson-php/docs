# Troubleshooting

## An [ext-ffi] disabled in your php.ini

```shell
FFI\Env\Exception\EnvironmentException: An [ext-ffi] disabled in your php.ini
```

> Make sure that the `ffi.enable` config in your `php.ini`
> file is set to `preload` (by default) or `true`.
> ```
> ffi.enable=preload ; default ffi option
> ```
{.note}

## An [ext-ffi] not available

```shell
FFI\Env\Exception\EnvironmentException: An [ext-ffi] not available
```

> Make sure that FFI extension is installed.

### Windows

1) Open your `php.ini` file.
   > The location of the configuration file can be found using the `php --ini` console command
   {.tip}
2) Find `extension=ffi` line and uncomment extension.

### Linux (Debian-like)

> sudo apt update
> sudo apt install php8.4-ffi

## Failed loading '.../libboson-linux-xxx.so'

> Make sure that GTK4 is installed.

### Linux (Debian-like)

```bash
apt install libgtk-4-1 libwebkitgtk-6.0-4
```

### Linux (Fedora)

```bash
dnf install gtk4 webkitgtk6.0
```

### Linux (FreeBSD)

```bash
pkg install webkit2-gtk4
```

## Illegal instruction (core dumped)

Linux prebuild was compiled with some assumptions, such as the presence of
`sse` and `avx2` instructions.

> Make sure your CPU supports `AVX2` (Intel Q2 2013, AMD Q2 2015)
> instructions set (`cat /proc/cpuinfo`).
>
> **Note:** VirtualBox may not support AVX/AVX2 when running under Hyper-V.
{.note}

## libEGL warning: egl: failed to create dri2 screen

> sudo apt-get install libnvidia-egl-wayland1
{.note}

## Failed to fully launch dbus-proxy: Child process exited with code 1

> This is [apparmor bug](https://bugs.launchpad.net/apparmor/+bug/2046844) and
> Ubuntu 24.04 is [also affected](https://bugs.launchpad.net/ubuntu/+source/apparmor/+bug/2060810).
>
> - It can be temporarily fixed by running code as `sudo`.
> - Or using following commands:
    >   ```bash
>   sudo sysctl -w kernel.apparmor_restrict_unprivileged_unconfined=0
>   sudo sysctl -w kernel.apparmor_restrict_unprivileged_userns=0
>   ```
{.note}
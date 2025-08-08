# Troubleshooting

## An [ext-ffi] disabled in your php.ini

You may encounter the following error message

```shell
FFI\Env\Exception\EnvironmentException: An [ext-ffi] disabled in your php.ini
```

To solve the problem make sure that the `ffi.enable` config in your `php.ini`
file is set to `preload` (by default) or `true`.


```ini
ffi.enable=preload ; default ffi option
```

## An [ext-ffi] not available

You may encounter the following error message

```shell
FFI\Env\Exception\EnvironmentException: An [ext-ffi] not available
```

To solve the problem make sure that FFI extension is installed.

**Windows**

1) Open your `php.ini` file.
   > The location of the configuration file can be found using the `php --ini` console command
   {.tip}
2) Find `extension=ffi` line and uncomment extension.

**Linux (Debian-like)**

> sudo apt update
> sudo apt install php8.4-ffi

## Failed loading '.../libboson-linux-xxx.so'

You may encounter the following error message

```
Uncaught FFI\Exception: Failed loading '.../libboson-linux-xxx.so'
```

To solve the problem make sure that GTK4 is installed.

**Ubuntu**

```bash
apt install libgtk-4-1 libwebkitgtk-6.0-4
```

**Debian**

```bash
apt install libgtk-4-1 libwebkitgtk-6.0-4
```

**Fedora**

```bash
dnf install gtk4 webkitgtk6.0
```

**FreeBSD**

```bash
pkg install webkit2-gtk4
```

## Undefined Symbol

You may encounter the following error message

```
Uncaught FFI\Exception: Failed loading '.../libboson-linux-xxx.so' 
(... undefined symbol: gtk_xxx)
```

To solve problem make sure you have the correct version GTK4 (4.12+) 
installed using the command (Debian example):
```bash
dpkg -l | grep libgtk-4
```

## Illegal instruction (core dumped)

Linux prebuild was compiled with some assumptions, such as the presence of
`SSE` and `AVX2` instructions.

Make sure your CPU supports `AVX2` (Intel Q2 2013, AMD Q2 2015)
instructions set (`cat /proc/cpuinfo`).

> **Note:** VirtualBox may not support AVX/AVX2 when running under Hyper-V.
{.warning}

## libEGL warning: egl: failed to create dri2 screen

To solve this problem, please install the appropriate drivers.

```bash
sudo apt-get install libnvidia-egl-wayland1
```

## Failed to fully launch dbus-proxy: Child process exited with code 1

This is [apparmor bug](https://bugs.launchpad.net/apparmor/+bug/2046844) and
Ubuntu 24.04 is [also affected](https://bugs.launchpad.net/ubuntu/+source/apparmor/+bug/2060810).

- It can be temporarily fixed by running code as `sudo`.
- Or using following commands:
```bash
sudo sysctl -w kernel.apparmor_restrict_unprivileged_unconfined=0
sudo sysctl -w kernel.apparmor_restrict_unprivileged_userns=0
```
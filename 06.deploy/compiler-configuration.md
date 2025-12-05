# Configuration

To create a build configuration, use the `init` command. The command will 
create the `boson.json` file in the root of the application with build settings.
It is not required for compilation, but allows better control over all stages 
of the build.

```json5
{
    //
    // The name of your application
    //
    "name": "app",

    //
    // An application entrypoint PHP file
    //
    "entrypoint": "index.php",

    // 
    // List of compilation targets
    //
    "target": [
        // macOS aarch64 build target
        {
            "type": "macos",
            "arch": "arm64",
            "ini": {
                "memory_limit": "256M"
            }
        },
        // Standalone PHP PHAR build target
        {"type": "phar"}
    ],

    //
    // An output build directory
    //
    "output": "./build",

    // 
    // Compiler's root directory
    //
    "root": ".",

    //
    // List of expected PHP extensions
    //
    "extensions": [
       "ctype",
       "sockets",
       "iconv"
    ],
  
    //
    // List of mounted files or directories (outside the assembly)
    //
    "mount": [
        "public"
    ],

    //
    // List of rules for including files (inside the assembly)
    //
    "build": {
        "finder": [
            {
                "directory": "src",
                "name": "*.php"
            },
            {
                "directory": "vendor",
                "not-directory": "vendor/boson-php/compiler",
                "name": "*.php"
            }
        ]
    },
   
    //
    // Additional options for the PHP interpreter
    //
    "ini": {
        "memory_limit": "128M"
    },
  
    //
    // Humbug Box version
    //
    "box-version": "4.6.7"
}
```


## Application Name

The `name` configuration section will contain name of your application.

It is used to create output executable file. For example, if you specify 
`"name": "example"`, the `example.exe` application will be created for 
the Windows platform (and `example` binaries for others).

```json5
{
    "name": "application name",
    // ...
}
```

> If the field is not specified, the `"app"` name will be used.
> ```json5
> {
>     "name": "app" // default value
> }
> ``` 
{.note}


## Application Entrypoint

The `entrypoint` configuration section will contain the path to the 
application entrypoint PHP file.

In the entrypoint field, you should specify the **relative** path to the file
that will be executed when the application is launched.

```json5
{
    "entrypoint": "path/to/entrypoint.php",
    // ...
}
```

> The entrypoint will be located on the same path inside the build as outside, 
> so you don't have to worry about paths (like 
> `require __DIR__ . '/vendor/autoload.php';`) breaks after building.


> If the field is not specified, the `"index.php"` will be used.
> ```json5
> {
>     "entrypoint": "./index.php" // default value
> }
> ```
{.note}


## Compilation Targets

The `target` configuration section should contain a list of compilation targets.

```json5
{
    "target": [
        {
            "type": "<name>",
            // ...additional options
        }
    ]
}
```


### Target Build Directory

You can explicitly specify the directory for the selected target using the
`output` configuration field.

```json5
{
    "target": [
        {
            "type": "<name>",
            "output": "example/path",
            // ...additional options
        }
    ]
}
```


### Executable Target

The executable target is used to create an executable file for the for one of 
the possible platforms: `windows`, `macos`, `linux`.

```json5
{
    "target": [
        {
            "type": "windows",
            // ...additional windows options
        },
        {
            "type": "macos",
            // ...additional macos options
        }
    ]
}
```

> By default, the directory (`output`) for this target is generated from the
> name and architecture: 
> - `./windows/amd64` for `"type": "windows", "arch": "amd64"`
> - or `./macos/arm64` for `type: "macos", "arch": "arm64"`
{.note}


#### Target Architecture

You can explicitly specify the required architecture for the specified 
platform in the `arch` field.

The following architectures are supported:

| Platform  | Architecture | Default |
|-----------|--------------|---------|
| `windows` | `amd64`      | ✔       |
| `linux`   | `amd64`      | ✔       |
| `linux`   | `arm64`      |         |
| `macos`   | `amd64`      | ✔       |
| `macos`   | `arm64`      |         |

If the field is not specified, the _default_ architecture will be selected, 
depending on the platform.

```json5
{
    "target": [
        {
            "type": "windows",
            "arch": "amd64",
            // ...additional windows/amd64 options
        },
        {
            "type": "macos",
            "arch": "arm64",
            // ...additional macos/arm64 options
        }
    ]
}
```


#### PHP Configuration

You can specify additional target options for the PHP interpreter using
the `ini` configuration field.

The key should be one of
[the available directive](https://www.php.net/manual/en/ini.list.php) names.
The value may be any scalar (`int`, `float`, `string` or `bool`) value.

```json5
{
    "target": [
        {
            "type": "windows",
            "ini": {
                "opcache.enable": true,
                "opcache.enable_cli": true,
                "opcache.jit_buffer_size": "64M"
            }
            // ...additional windows/amd64 options
        },
        {
            "type": "macos",
            "ini": {
                "opcache.enable": false
            }
            // ...additional macos/arm64 options
        }
    ]
}
```

> To specify global PHP configuration (to all compilation targets), use the 
> [global `ini` configuration section](../06.deploy/compiler-configuration.md#global-php-configuration).
> ```json5
> {
>     "ini": {
>         "opcache.enable": false,
>         "memory_limit": "128M"
>     },
>     "target": [
>         {
>             "type": "windows",
>             // override ini options for windows
>             "ini": { 
>                 "opcache.enable": true
>             }
>         }
>     ]
> }
> ```


#### Target SFX

By default, each target is built using pre-built SFX. You can specify the 
build explicitly using the path to custom SFX.

```json5
{
    "target": [
        {"type": "windows", "sfx": "./build/sfx/php-windows-amd64.sfx"},
        {"type": "macos", "sfx": "./build/sfx/php-macos-arm64.sfx"}
    ]
}
```

> More information about assembling your own SFXs can be found 
> [in the "Custom Edition" section](../06.deploy/compiler-configuration.md#custom-edition).
{.note}


### PHAR Target

A PHAR target is used to create a standalone PHP archive (PHAR) file.

```json5
{
    "target": [
        {
            "type": "phar",
            // ...additional PHAR options
        }
    ]
}
```


### Custom Target

You can specify your own target using the class path in the `type` section 
of the configuration.

```json5
{
    "target": [
        {
            "type": "My\\CustomTargetFactory",
            // ...additional options
        }
    ]
}
```

Such a class must implement `Boson\Component\Compiler\Target\TargetFactoryInterface`
interface:

```php
<?php

use Boson\Component\Compiler\Configuration;
use Boson\Component\Compiler\Target\TargetFactoryInterface;
use Boson\Component\Compiler\Target\Target;

final readonly class CustomTargetFactory implements TargetFactoryInterface
{
    public function create(array $input, Configuration $config): CustomTarget
    {
        // $input - array of configuration options for the target
        // $config - parsed compiler configuration
        return new CustomTarget(
            type: 'custom',
            output: $input['output'] ?? 'custom_directory',
            config: $input,
        );
    }
}

final readonly class CustomTarget extends Target
{
    public function compile(Configuration $config): void
    {
        // Example compilation task usage
        Task::run($config, new CopyFileTask(
            // Copy PHAR archive
            sourcePathname: $config->pharPathname,
            // To the output directory + filename
            targetPathname: $this->getBuildDirectory($config) 
                . '/' . \basename($config->pharPathname),
        ));

        // Other compilation tasks usage
        Task::run($config, new DoSomethingElse(...));
    }
}
```


## Build Directory

The `build` configuration section will contain the path to the 
output directory.

The **relative** path is specified in which all assembly files and the
result of the assembly itself will be placed.

```json5
{
    "build": "./var/build",
    // ...
}
```

> If the field is not specified, the `"build"` directory will be used.
> ```json5
> {
>     "build": "./build" // default value
> }
> ```
{.note}


> Please note that the final directory is generated from `build`
> + `target[x].output` fields.
{.warning}`


## Embedded Files

The `build` configuration section may contain a list of files to 
include in assembly.

This field contains an object with a set of rules. 
Available fields of the object:

- `"files"` – List of files to include.
- `"directories"` – List of directories to include.
- `"finder"` – List of rules (filters) to include.

### Specifying Embedded Files

The `"files"` section specifies a list (array) of individual files 
to include in the assembly.

```json5
{
    "build": {
        "files": [
            "./path/to/file.php",
            "./some/awesome.jpg",
            // ...
        ],
        // ...
    },
    // ...
}
```

> An entrypoint file is automatically included in this list, 
> it is not necessary to specify it separately.


### Specifying Embedded Directories

The `"directories"` section specifies a list (array) of directories
to include in the assembly.

```json5
{
    "build": {
        "directories": [
            "./public",
            "./resources",
            // ...
        ],
        // ...
    },
    // ...
}
```

> Specifying a directory includes **all** files, including 
> temporary ones or those in `.gitignore`.
{.note}


### Advanced Embedding

The `"finder"` section specifies a list (array) of 
[finder-like](https://symfony.com/doc/current/components/finder.html) rules 
to include in the assembly.

```json5
{
    "build": {
        "finder": [
            {
                // "string" or ["string"]
                "directory": "vendor",

                // "string" or ["string"]
                "not-directory": "vendor/phpunit",

                // "string" or ["string"]
                "name": "*.php",

                // "string" or ["string"]
                "not-name": "Test.php"
            },
            // ...
        ],
        // ...
    },
    // ...
}
```

The `"finder"` may contain an array of objects with, `"name"`, `"not-name"`, 
`"directory"` and `"not-directory"` fields.

#### `name` field format

Filters files by name. All files matching the 
specified rule will be included in the build.

> The `name` field may be defined as `string` or 
> `array` of strings

**mask**

You may specify a mask where an asterisk means any 
occurrence of any number of characters
```*.php```
```index*```

**pattern**

You can specify a regular expression to check the file name. Such 
an expression must start and end with the `/` characters.
```/\.php$/```
```/^index.*/```

> The field only checks the file **name**, not the file path.
{.warning}
</procedure>

> To exclude from the selection by names, use the `"not-name"` field 
> with the same capabilities as `"name"` field.
{.note}

#### `directory` field format

Specifies the directory in which to search for files to include.

> The `directory` field may be defined as `string` or 
> `array` of strings

You may define real path to directory.
```
./path/to/directory
```

**mask**

You may use `*` as a wildcard character to search in the 
directories matching a pattern (each pattern has to resolve to at 
least one directory path).
```
./path/to/*/*/dir
```


> To exclude directories, use the `"not-directory"` field 
> with the same capabilities as `"directory"` field.
{.note}


## Mounted Files

You can specify a list of files or directories to be mounted **outside** the 
assembly using the `mount` configuration field.

If you need to write data somewhere, you should specify that 
path in the mount rules.

```json5
{
    "mount": [
        "./var/cache/",
        "./path/to/file.png"
    ]
}
```

The specified directory (or file) will then be mounted outside the 
executable file and will be writable.

You don't need to do anything special to access these directories; they are 
accessed in the same way as any other directory.


## PHP Extensions

You can specify a list of PHP extensions to be loaded using the `extensions` 
configuration field.

```json5
{
    "extensions": [
        "ctype",
        "sockets",
        "iconv"
    ]
}
```

> This configuration section only applies to all [executable targets](../06.deploy/compiler-configuration.md#executable-target).
{.warning}

There are currently two prebuild editions available: 
- Minimal 
- Standard

Depending on which extensions are required, this particular pre-built 
edition will be selected.

### Minimal Edition

This edition contains the following list of PHP extensions:

- `core`
- `ffi`
- `phar`
- `reflection`
- `spl`
- `opcache`
- `ctype`
- `date`
- `filter`
- `hash`
- `iconv`
- `json`
- `pcre`
- `random`
- `shmop`
- `sockets`
- `standard`
- `zlib`


### Standard Edition

This edition contains the following list of PHP extensions:

- `core`
- `ffi`
- `pdo`
- `phar`
- `reflection`
- `spl`
- `opcache`
- `ctype`
- `curl`
- `date`
- `dom`
- `filter`
- `hash`
- `iconv`
- `json`
- `libxml`
- `mbstring`
- `openssl`
- `pcre`
- `pdo_sqlite`
- `random`
- `shmop`
- `sockets`
- `sodium`
- `sqlite3`
- `standard`
- `xml`
- `zlib`


### Custom Edition

To build a custom edition, please use GitHub CI:
1) Clone this repository: [boson-php/backend-src](https://github.com/boson-php/backend-src)
2) Open "Actions" tab:
  ![Actions Tab](https://habrastorage.org/webt/yd/tz/j6/ydtzj6pmchvov9v8rrv8dzdovwy.png)
  - Select "CI on Unix" for `Linux` or `macOS`
  - Select "CI on x86_64 Windows" for `Windows`
3) Open "Run Workflow" dropdown:
   ![Run Workflow](https://habrastorage.org/webt/_v/1e/yh/_v1eyhe5u8nadu7aiyzubbyp81o.png)
4) Run the workflow and download the "php-micro" artifact:
   ![Artifact](https://habrastorage.org/webt/k8/wi/mx/k8wimx6r_xpavy2etmrmpehppho.png)
5) Specify the path to the downloaded SFX within a specific build target, for example:
   ```json5
   {
       "target": [
           {
               "type": "macos",
               "sfx": "./build/sfx/php-micro-macos-arm64.sfx"
           }
       ]
   }
   ```

## Global PHP Configuration

You can specify additional options for the PHP interpreter using 
the `ini` configuration field.

The key should be one of 
[the available directive](https://www.php.net/manual/en/ini.list.php) names. 
The value may be any scalar (`int`, `float`, `string` or `bool`) value.

```json5
{
    "ini": {
        "memory_limit": "128M",
        "opcache.jit_buffer_size": "32M"
    }
}
```


> You may use environment variables inside PHP configuration values.
> ```json5
> {
>     "ini": {
>         "memory_limit": "${BOSON_MEMORY_LIMIT}"
>     }
> }
> ```
> 
> In addition, default values are available for environment variables:
> ```json5
> {
>     "ini": {
>         "memory_limit": "${BOSON_MEMORY_LIMIT:-128M}"
>     }
> }
> ```


> This configuration section only applies to all [executable targets](../06.deploy/compiler-configuration.md#executable-target).
{.warning}
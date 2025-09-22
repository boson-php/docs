# Configuration

To create a build configuration, use the `init` command. The command will 
create the `boson.json` file in the root of the application with build settings.
It is not required for compilation, but allows better control over all stages 
of the build.

```json5
{
    //
    // The name of your application.
    //
    "name": "app",
  
    // 
    // List of build architectures.
    //
    "arch": [ "amd64", "aarch64" ],
  
    //
    // List of build platforms.
    //
    "platform": [ "windows", "linux", "macos" ],
  
    //
    // An application entrypoint PHP file.
    //
    "entrypoint": "index.php",

    //
    // An output build directory.
    //
    "output": "./build",

    //
    // List of rules for including files inside the assembly.
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
    }
}
```


## `name`

The name of your application.

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
{.note}


## `arch`

List of build architectures.

You can explicitly specify the CPU architectures your application will be built 
for.

**Available options:**
 - `amd64` (or `x86_64`)
 - `aarch64` (or `arm64`)

```json5
{
    "arch": [ "amd64", "aarch64" ],
    // ...
}
```

> If the field is not specified (including empty array), all available 
> architectures will be used.
{.note}


## `platform`

List of build platforms.

You can explicitly specify a list of operating systems for which your 
application will be compiled.

**Available options:**
 - `windows` (or `win`/`win32`/`win64`)
 - `linux`
 - `macos` (or `darwin`)

```json5
{
    "platform": [ "windows", "linux", "macos" ],
    // ...
}
```

> If the field is not specified (including empty array), all available 
> platforms will be used.
{.note}

## `entrypoint`

An application entrypoint PHP file.

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
{.note}

## `output`

An output build directory.

The **relative** path is specified in which all assembly files and the
result of the assembly itself will be placed.

```json5
{
    "build": "./var/build",
    // ...
}
```

> The build result will be located in this directory depending on the
> specified platforms and architectures.
> 
> ```
> ~/<build-directory>/<platform>/<arch>/...
> ```
> 
> For example, for Windows x64 with the specified build 
> directory `"./var/build"` and `"app"` 
> application name:
> 
> ```
> ~/var/build/windows/amd64/app.exe
> ~/var/build/windows/amd64/libboson-windows-x86_64.dll
> ~/var/build/windows/amd64/...etc
> ```

> If the field is not specified, the `"build"` directory will be used.
{.note}

## `build`

List of rules for including files inside the assembly.

This field contains an object with a set of rules. 
Available fields of the object:

- `"files"` – List of files to include.
- `"directories"` – List of directories to include.
- `"finder"` – List of rules (filters) to include.

### `build.files`

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


### `build.directories`

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


### `build.finder`

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


## `mount`

List of mounted files or directories.

After the application is built, all files are placed inside the executable 
file. You can read them, but they **CAN NOT** be written to.

If you need to write data somewhere, you should specify that 
path in the mount rules.

```json5
{
    "mount": [
        "./var/cache/"
    ]
}
```

The specified directory (or file) will then be mounted outside the 
executable file and will be writable.

You don't need to do anything special to access these directories; they are 
accessed in the same way as any other directory.

## `ini`

Additional options for the PHP interpreter.

You can specify additional options for the interpreter using 
the settings object.

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
# Extensions

Each window contains a list of extensions that enhance the window's 
functionality. A list of all available extensions is configured in the
`WindowCreateInfo::$extensions` field:

```php
new Boson\Window\WindowCreateInfo(
    extensions: [
        ...Boson\Window\WindowCreateInfo::DEFAULT_WINDOW_EXTENSIONS,
        // ...
        new ExampleCustomExtension(),
        new ExampleAnotherExtension(),
    ],
);
```

In addition, you can use the `WindowCreateInfo::extensions()` method for 
more convenient filtering and adding extensions:

```php
new Boson\Window\WindowCreateInfo(
    extensions: Boson\Window\WindowCreateInfo::extensions(
        with: [ new ExampleCustomExtension() ],
        except: [ ExampleDisabledExtension::class ],
    ),
),
```

You can completely disable all available extensions by specifying an empty list:

```php
new Boson\Window\WindowCreateInfo(extensions: []);
```

Boson application includes a list of both built-in extensions (defined in
`Boson\Window\WindowCreateInfo::DEFAULT_WINDOW_EXTENSIONS` constant) and
external ones that can be installed separately using `composer install <ext-name>`.

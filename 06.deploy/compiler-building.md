# Building

To build an executable application file, you should use the `compile` command.

```shell
php vendor/bin/boson compile
```

After running the command you will get something like the following result:

<video controls poster="/assets/compilation.png?v2">
  <source src="/assets/compilation.mp4?v2" type="video/mp4" />
  <source src="/assets/compilation.webm?v2" type="video/webm" />
  Your browser doesn't support HTML5 video tag.
</video>

This command will compile your application into single executable file.
This is enough to distribute the application. NO dependencies (`php`, `node`,
`electron`, etc.) are required anymore. Everything you need will already be
included inside and ready to work!

## Cross-compilation

The following platforms and architectures are available 
for building the application:

- `Windows`
  - `amd64` (`x86_64`)
- `Linux`
  - `amd64` (`x86_64`)
  - `aarch64` (`arm64`)
- `macOS`
  - `amd64` (`x86_64`)
  - `aarch64` (`arm64`)

> The same applies to `boson-php/runtime`. At the moment, only 
> these platforms are supported, even if you distribute the application 
> as source code, without building.
{.note}
# URI

The component provides a set of URI structures.

> This component already included in the `boson-php/runtime`,
> so no separate installation is required when using the runtime.
{.note}


## Installation

Via [Composer](https://getcomposer.org/doc/01-basic-usage.md#installing-dependencies):

```bash
composer require boson-php/uri
```

**Requirements:**

* `PHP ^8.4`

## URI Class

The URI object is a DTO-like (value object) containing information about the 
Scheme, Authority, Path, Query parameters and Fragment.

```mermaid
flowchart LR
    Uri --> |&nbsp;scheme&nbsp;| Scheme
    Uri --> |&nbsp;authority&nbsp;| Authority
    Uri --> |&nbsp;path&nbsp;| Path
    Uri --> |&nbsp;query&nbsp;| Query
    Uri --> |&nbsp;fragment&nbsp;| Fragment
    Authority --> |&nbsp;user info&nbsp;| UserInfo
    Authority --> |&nbsp;host&nbsp;| Host
    Authority --> |&nbsp;port&nbsp;| Port
    UserInfo --> |&nbsp;user&nbsp;| User
    UserInfo --> |&nbsp;password&nbsp;| Password
    Path --> |"&nbsp;sements[0]&nbsp;"| Segment1
    Path --> |"&nbsp;sements[1]&nbsp;"| Segment2
    Query --> |"&nbsp;parameters[0]&nbsp;"| Parameter1
    Query --> |"&nbsp;parameters[1]&nbsp;"| Parameter2
    Parameter1 --> |&nbsp;key&nbsp;| Parameter1Key
    Parameter1 --> |&nbsp;value&nbsp;| Parameter1Value
    Parameter2 --> |&nbsp;key&nbsp;| Parameter2Key
    Parameter2 --> |&nbsp;value&nbsp;| Parameter2Value
    
    Uri["abc://user:pass\@example.com:123/path/data?k=val&k2=val2#frag"]
    Scheme["abc"]
    Authority["user:pass\@example.com:123"]
    Path["path/data"]
    Query["k=val&k2=val2"]
    Fragment["frag"]
    UserInfo["user:pass"]
    User["user"]
    Password["pass"]
    Host["example.com"]
    Port["123"]
    Segment1["path"]
    Segment2["data"]
    Parameter1["k=val"]
    Parameter2["k2=val2"]
    Parameter1Key["k"]
    Parameter1Value["val"]
    Parameter2Key["k2"]
    Parameter2Value["val2"]
```

You can use the properties to access the corresponding URI component.

- `$uri->scheme` – Contains a schema component. May be `null` if the schema is 
  not defined in the URI.
- `$uri->authority` – Contains an authority component. May be `null` if the 
  host (and other URI components) is not defined in the URI.
- `$uri->path` – Contains a path component.
- `$uri->query` – Contains a query component.
- `$uri->fragment` – Contains a path component. May be `null` if the fragment
  is not defined in the URI.

In addition to the properties listed above, the `Uri` object contains a set of 
"facade" properties that delegate calls to internal components, providing a 
more convenient way to obtain frequently used data.

- `$uri->user` – An alias of `$uri->authority?->userInfo?->user`.
- `$uri->password` – An alias of `$uri->authority?->userInfo?->password`.
- `$uri->host` – An alias of `$uri->authority?->host`.
- `$uri->port` – An alias of `$uri->authority?->port`.

```php
echo $uri . "\n";
// abc://user:pass@example.com:123/path/data?k=val&k2=val2#frag

echo $uri->scheme . "\n";
// abc

echo $uri->authority . "\n";
// user:pass@example.com:123

echo $uri->path . "\n";
// /path/data

echo $uri->query . "\n";
// k=val&k2=val2

echo $uri->fragment . "\n";
// frag

echo $uri->user . "\n";
// user

echo $uri->password . "\n";
// pass

echo $uri->host . "\n";
// example.com

echo $uri->port . "\n";
// 123
```

## Scheme

Scheme is a part of the [Uri](../07.components/uri.md#uri-class), but
you can use them separately. For this, Boson component provides an enum-like
`Boson\Component\Uri\Component\Scheme` class containing a set of most popular
schemes (protocols):

- `Scheme::Http` – The [Hypertext Transfer Protocol](https://en.wikipedia.org/wiki/HTTP) scheme
- `Scheme::Https` – The [Hypertext Transfer Protocol Secure](https://en.wikipedia.org/wiki/HTTPS) scheme
- `Scheme::Data` – The [Data URI](https://en.wikipedia.org/wiki/Data_URI_scheme) scheme
- `Scheme::File` – The [File URI](https://en.wikipedia.org/wiki/File_URI_scheme) scheme
- `Scheme::Ftp` – The [File Transfer Protocol](https://en.wikipedia.org/wiki/File_Transfer_Protocol) scheme
- `Scheme::Gopher` – The [Gopher Protocol](https://en.wikipedia.org/wiki/Gopher_(protocol)) scheme
- `Scheme::Ws` – The [WebSocket Protocol](https://en.wikipedia.org/wiki/WebSocket) scheme
- `Scheme::Wss` – The [WebSocket Protocol Secure](https://en.wikipedia.org/wiki/WebSocket) scheme

Since the `Scheme` class implements behavior similar to
[PHP enums](https://www.php.net/manual/ru/language.types.enumerations.php),
you also have access to the `from(), `tryFrom()` and `cases()` methods.

> Due to many technical limitations of the PHP (for example,
> [unable to use properties](https://externals.io/message/126332),
> [unable to define the `__toString()` method](https://externals.io/message/124991),
> unable to override `from()` & `tryFrom()` methods, etc.),
> this class cannot be implemented using the classic PHP enum.
{.warning}

```php
use Boson\Component\Uri\Component\Scheme;

echo Scheme::from('http');
// http

echo Scheme::from('wtf');
// Uncaught ValueError: "wtf" is not a valid backing value for
// enum-like Boson\Component\Uri\Component\Scheme

echo Scheme::tryFrom('wtf'); 
// null
```

> Please note that the `from()` and `tryFrom()` methods
> are case-insensitive. Scheme value will be lowercased.
{.note}

```php
use Boson\Component\Uri\Component\Scheme;

foreach (Scheme::cases() as $scheme) {
    echo $scheme . "\n";
}

//
// Expected Output:
//
//   http
//   https
//   data
//   file
//   ftp
//   gopher
//   ws
//   wss
//
```
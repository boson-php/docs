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
`scheme`, `authority`, `path`, `query` parameters and `fragment`.

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

The URI object contains a `__toString()` method, so it can be passed 
as any `Stringable` value or can be converted to a `string`.

> Here and below, URI `abc://user:pass@example.com:123/path/data?k=val&k2=val2#frag` 
> will be used as an example.

```php
echo $uri;

//
// Expected Output:
//
//   abc://user:pass@example.com:123/path/data?k=val&k2=val2#frag
//
```

You can use the corresponding properties to access the data located
inside the `Uri`.

- `$uri->scheme` – Contains a scheme component. May be `null` if the scheme is 
  not defined in the URI.
- `$uri->authority` – Contains an authority component. May be `null` if the 
  host (and other URI components) is not defined in the URI.
- `$uri->path` – Contains a path component.
- `$uri->query` – Contains a query component.
- `$uri->fragment` – Contains a path component. May be `null` if the fragment
  is not defined in the URI.

```php
echo $uri->scheme . "\n";

//
// Expected Output:
//
//   abc
//


echo $uri->authority . "\n";

//
// Expected Output:
//
//   user:pass@example.com:123
//


echo $uri->path . "\n";

//
// Expected Output:
//
//   /path/data
//


echo $uri->query . "\n";

//
// Expected Output:
//
//   k=val&k2=val2
//


echo $uri->fragment . "\n";

//
// Expected Output:
//
//   frag
//
```

In addition to the properties listed above, the `Uri` object contains a set of 
"facade" properties that delegate calls to internal components, providing a 
more convenient way to obtain frequently used data.

- `$uri->user` – An alias of `$uri->authority?->userInfo?->user`.
- `$uri->password` – An alias of `$uri->authority?->userInfo?->password`.
- `$uri->host` – An alias of `$uri->authority?->host`.
- `$uri->port` – An alias of `$uri->authority?->port`.

```php
echo $uri->user . "\n";

//
// Expected Output:
//
//   user
//


echo $uri->password . "\n";

//
// Expected Output:
//
//   pass
//


echo $uri->host . "\n";

//
// Expected Output:
//
//   example.com
//


echo $uri->port . "\n";

//
// Expected Output:
//
//   123
//
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

The `Scheme` is a value object that contains a `__toString()` method, so it can
be passed as any `Stringable` value or can be converted to a `string`.

```php
echo $uri->scheme . "\n";

//
// Expected Output:
//
//   abc
//
```

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

//
// Expected Output:
//
//   http
//

echo Scheme::from('wtf');

//
// Expected Output:
//
//   Uncaught ValueError: "wtf" is not a valid backing value for
//   enum-like Boson\Component\Uri\Component\Scheme
//

echo get_debug_type(Scheme::tryFrom('wtf'));


//
// Expected Output:
// 
//   null
//
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


### Scheme Default Port

Each built-in scheme may also contain a port defined by
default for that scheme.

The port property may contain `null` if the scheme does not contain a 
known default port.

> Note that default ports are only available for built-in schemes. A 
> [user-defined schemes](../07.components/uri.md#user-defined-scheme) do not 
> contain this property.

```php
use Boson\Component\Uri\Component\Scheme;

echo Scheme::Https->port;  // 443

echo Scheme::Gopher->port; // 70
```


### User Defined Scheme

In addition to the [basic schemes](../07.components/uri.md#scheme), you may 
need to create your own.

To do this, you should create an instance of the `Scheme` object.

```php
use Boson\Component\Uri\Component\Scheme;

echo new Scheme('Boson'); // boson
```

> The `Boson`s constructor is case-sensitive. Scheme value will be lowercased.


## Authority

Authority is a simple value object containing information about
the host, port, and user info of the [Uri](../07.components/uri.md#uri-class).

As well as URI contains a `__toString()` method, so it can be passed
as any `Stringable` value or can be converted to a `string`.

```php
echo $uri->authority . "\n";
// user:pass@example.com:123
```

You can use the corresponding properties to access the data located 
inside the `Authority` URI's component.

- `$authority->userInfo` – Contains user info component. May be `null` in case 
  of user info is not defined in authority.
- `$authority->host` – Contains host component.
- `$authority->port` – Contains port component. May be `null` in case
  of port is not defined in authority.

```php
echo $uri->authority->userInfo . "\n";

//
// Expected Output:
//
//   user:pass


echo $uri->authority->host . "\n";

//
// Expected Output:
//
//   example.com
//


echo $uri->authority->port . "\n";

//
// Expected Output:
//
//   123
//
```

Authority also contains a set of "facade" properties that provide quick 
access to embedded data.

- `$authority->user` – An alias of `$authority->userInfo->user`.
- `$authority->password` – An alias of `$authority->userInfo->password`.

```php
echo $uri->authority->user . "\n";

//
// Expected Output:
//
//   user


echo $uri->authority->password . "\n";

//
// Expected Output:
//
//   pass
//
```

> This data can also be obtained directly from the
> [Uri](../07.components/uri.md#uri-class) facade properties.


## User Info

User Info is a value object containing information about user and password 
in the URI (in [authority](../07.components/uri.md#authority) URI's component).

The `UserInfo` object contains a `__toString()` method, so it can be passed
as any `Stringable` value or can be converted to a `string`.

```php
echo $uri->authority->userInfo . "\n";

//
// Expected Output:
//
//   user:pass
//
```

You can use the corresponding properties to access the data located
inside the `UserInfo` URI's component.

```php
echo $userInfo->user . "\n";

//
// Expected Output:
//
//   user
//


echo $userInfo->password . "\n";

//
// Expected Output:
//
//   pass
//
```

> This data can also be obtained directly from the 
> [Uri](../07.components/uri.md#uri-class) and
> [Authority](../07.components/uri.md#authority) facade properties.


## Path

Path is a value object containing information about the path 
in [Uri](../07.components/uri.md#uri-class).

The `Path` object contains a `__toString()` method, so it can be passed
as any `Stringable` value or can be converted to a `string`.

```php
echo $uri->path . "\n";

//
// Expected Output:
//
//   /path/data
//
```

In addition to the `Path`'s segments, it contains several 
additional information fields.

- `$path->isAbsolute` – Contains a `bool` value indicating whether the 
  Uri's path starts with a slash `/`.
  > ```php
  > if ($path->isAbsolute) {
  >     echo $path === '/path/data';
  > } else {
  >     echo $path === 'path/data';
  > }
  > ```
- `$path->hasTrailingSlash` – Contains a `bool` value indicating whether the
  Uri's path ends with a slash `/`
  > ```php
  > if ($path->hasTrailingSlash) {
  >     echo $path === 'path/data/';
  > } else {
  >     echo $path === 'path/data';
  > }
  > ```


### Path Segments

The `Path` object can be split into segments and provides `Traversable` and 
`Countable` capabilities.

```php
echo $path;

//
// Expected Output:
//
// /path/data
//

foreach ($path as $segment) {
    echo $segment;
}

//
// Expected Output:
//
//   path
//   data
//


echo count($path);

//
// Expected Output:
//
//   2
//
```


### Path Comparison

To compare paths, you can compare `Path` objects. However, in this case, in the 
case of two different URI objects, the result will be `false`.

```php
echo $uri1; // http://example.com/path
echo $uri2; // http://example.com/path

// Check that "path" is IDENTICAL to "path"
var_dump($uri1->path === $uri2->path);

//
// Expected Output:
//
//   false
//
```

To compare the structure of objects without taking into account the
identity of instances, the equivalence `==` operator should be used.

```php
echo $uri1; // http://example.com/path
echo $uri2; // http://example.com/path
echo $uri3; // http://example.com/path/


// Check that "path" is EQUAL to "path"
var_dump($uri1->path == $uri2->path);

//
// Expected Output:
//
//   true
//


// Check that "path" is EQUAL to "path/"
var_dump($uri1->path == $uri3->path);

//
// Expected Output:
//
//   false
//
```

To compare only the segments of a `Path` object, the `equals()` 
method should be used.

```php
echo $uri1; // http://example.com/path
echo $uri2; // http://example.com/path/


// Comparison of path's segments
var_dump($uri1->path->equals($uri2->path));

//
// Expected Output:
//
//   true
//
```


### Query

Query is a value object containing information about the query parameters
in [Uri](../07.components/uri.md#uri-class).

The `Query` object contains a `__toString()` method, so it can be passed
as any `Stringable` value or can be converted to a `string`.

```php
echo $uri->query . "\n";

//
// Expected Output:
//
//   k=val&k2=val2
//
```

### Getting Parameter

To get any parameter by its name, use the `get()` method. This method can only 
return a `string` or `null`.

```php
$k2 = $uri->query->get('k2');

//
// Expected Result:
//
//   $k2 is "val2"
//


$k1 = $uri->query->get('k1');

//
// Expected Result:
//
//   $k1 is null
//
```

As a second optional argument, it is also possible to specify a default value 
in the case that the expected query parameter is not passed.

```php
$k2 = $uri->query->get('k2', 'undefined');

//
// Expected Result:
//
//   $k2 is "val2"
//


$k1 = $uri->query->get('k1', 'undefined');

//
// Expected Result:
//
//   $k1 is "undefined"
//
```

If the query parameter contains an array, the `get()` method will return the 
first element of this array.

```php
// $uri is "http://example.com?key[]=value1&key[]=value2"
$value = $uri->query->get('key');

//
// Expected Result:
//
//   $value is "value1"
//


// $uri is "http://example.com?key[a]=value1&key[b]=value2"
$value = $uri->query->get('key');

//
// Expected Result:
//
//   $value is "value1"
//
```


### Integer Parameters

When getting a numeric value using the `get()` method, a `string` will always 
be returned. If you need an `int` value, you should use the `getAsInt()` method.

```php
// $uri is http://example.com?key=42
$value = $uri->query->getAsInt('key');

//
// Expected Result:
//
//   $value is 42
//
```

If the query parameter contains a value that cannot be converted to an `int`, 
then `getAsInt()` will return `null`, even if such a parameter is present.

```php
// $uri may be:
// - http://example.com?key=non-numeric
// - http://example.com?key=0.42
// - http://example.com?key=2e4
// - http://example.com?key=042
$value = $uri->query->getAsInt('key');

//
// Expected Result (for all URI variants):
//
//   $value is null
// 
```


### Array Parameters

To get parameters as `array`, you should use the `getAsArray()` method. 
Regardless of the type of the parameter, the method will always return 
an `array`.

```php
// $uri is http://example.com?key[]=value&key[a]=value2
$value = $uri->query->getAsArray('key');

//
// Expected Result:
//
//   $value is [0 => 'value', 'a' => 'value2']
//


// $uri is http://example.com?key=value
$value = $uri->query->getAsArray('key');

//
// Expected Result:
//
//   $value is [0 => 'value']
//
```
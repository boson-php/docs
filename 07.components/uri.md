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

- `$uri->scheme` – Contains the value object of the schema component. May be 
  `null` if the schema is not defined in the URI.
- `$uri->authority` – Contains the value object of the authority component.
  May be `null` if the host (and other URI components) is not defined in the URI.
- `$uri->path` – Contains the value object of the path component.
- `$uri->query` – Contains the value object of the query component.
- `$uri->fragment` – Contains the string value of the path component. May be
  `null` if the fragment is not defined in the URI.

In addition to the properties listed above, the `Uri` object contains a set of 
"facade" properties that delegate calls to internal components, providing a 
more convenient way to obtain frequently used data.

- `$uri->user` – An alias of `$uri->authority?->userInfo?->user` property.
  May provide `null` value.
- `$uri->password` – An alias of `$uri->authority?->userInfo?->password` property.
  May provide `null` value.
- `$uri->host` – An alias of `$uri->authority?->host` property.
  May provide `null` value.
- `$uri->port` – An alias of `$uri->authority?->port` property.
  May provide `null` value.

```php
echo $uri . "\n";            // abc://user:pass@example.com:123/path/data?k=val&k2=val2#frag

echo $uri->scheme . "\n";    // abc
echo $uri->authority . "\n"; // user:pass@example.com:123
echo $uri->path . "\n";      // /path/data
echo $uri->query . "\n";     // k=val&k2=val2
echo $uri->fragment . "\n";  // frag

echo $uri->user . "\n";      // user
echo $uri->password . "\n";  // pass
echo $uri->host . "\n";      // example.com
echo $uri->port . "\n";      // 123
```
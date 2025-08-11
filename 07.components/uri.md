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

The URI object is a DTO containing information about the Scheme, Authority, Path, 
Query parameters and Fragment.

```mermaid
flowchart
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

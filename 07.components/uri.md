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
    Uri e1@--> Scheme
    Uri e2@--> Authority
    Uri e3@--> Path
    Uri e4@--> Query
    Uri e5@--> Fragment

    e1@{ animation: fast }
    e2@{ animation: fast }
    e3@{ animation: fast }
    e4@{ animation: fast }
    e5@{ animation: fast }

    Uri["abc://user:pass@example.com:123/path/data?k=val&k2=val2#frag"]
    Scheme["abc"]
    Authority["user:pass@example.com:123"]
    Path["path/data"]
    Query["k=val&k2=val2"]
    Fragment["frag"]
```

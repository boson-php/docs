
# Schemes API Events

Any configured schema can send its own events (and intentions), which allow 
interaction with requests to specified schemas.


> More information about events can be found in the <a href="events.md">events 
> documentation</a>.
{.note}

## Request Intention

An `Boson\WebView\Api\Schemes\Event\SchemeRequestReceived` intention processing of user schemes
registered [in the configuration](application-configuration.md#intercepted-schemes).

```php
class SchemeRequestReceived<WebView> 
{
    public readonly Boson\Http\RequestInterface $request;
    
    public ?Boson\Http\ResponseInterface $response = null;
}
```

- `$request` - Custom protocol request instance.
- `$response` - An optional response instance containing the body string.


> If intention is cancelled, then the response will be rejected (aborted).


> An intention is **only** called for registered (in configuration) schemes.
{.warning}
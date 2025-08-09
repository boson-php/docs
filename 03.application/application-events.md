# Events

The application will automatically emit the following events (and intentions)
during its lifecycle.

To subscribe to events, you can use direct access to the
[event listener](../02.architecture/events.md#event-listener).

```php
$app->addEventListener(Event::class, function (Event $e) {
    var_dump($e);
});
```

The application instance also supports a more convenient and simple way of
registering events using the `on()` method.

```php
$app->on(function (Event $event): void {
    var_dump($event);
});
```

> More information about events can be found in the
> [events documentation](../02.architecture/events.md).
{.note}


## Starting Intention

An `Boson\Event\ApplicationStarting` intention to start the application.

```php
class ApplicationStarting<Application>
```

> If it is cancelled, the application will not be launched.


## Started Event

An `Boson\Event\ApplicationStarted` event fired after the application has been
launched and the `Boson\Event\ApplicationStarting` intention has not been
cancelled.

```php
class ApplicationStarted<Application>
```

## Stopping Intention

An `Boson\Event\ApplicationStopping` intention to stop the application.

```php
class ApplicationStopping<Application>
```

> If it is cancelled, the application will not be stopped.


## Stopped Event

An `Boson\Event\ApplicationStopped` event fired after the application has been
stopped and the `Boson\Event\ApplicationStopping` intention has not been
cancelled.

```php
class ApplicationStopped<Application>
```

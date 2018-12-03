# eventbus

Yet another Go (golang) eventbus implementation

## Installation

Using `go get`:

```
go get -u github.com/ispringteam/eventbus
```

Using `dep`:

```
dep ensure -add github.com/ispringteam/eventbus
```

## Usage

First, import this library:

```go
import (
    "github.com/ispringteam/eventbus"
)
```

Then define your event struct with method `EventID() eventbus.EventID`:

```go
type solarEclipseEvent struct {
	duration time.Duration
}

func (e *solarEclipseEvent) EventID() EventID {
	return eventSolarEclipse
}
```

Create the bus (it's better to create bus in the `main` function) and feel free to Subscribe, Publiish and Unsubscribe:

```go
func main() {
	bus := New()
	duration := 100 * time.Second

	id := bus.Subscribe(eventSolarEclipse, func(e Event) {
		se := e.(*solarEclipseEvent)
		fmt.Printf("solar eclipse for %v seconds", se.duration)
	})
	bus.Publish(&solarEclipseEvent{
		duration: duration,
	})
    bus.Unsubscribe(id);
}
```

## Why yet another EventBus?

This package is inspired by [github.com/asaskevich/EventBus](https://github.com/asaskevich/EventBus), but has differences:

* no event bus over network, no async event bus: only process-local synchronous event handlers are supported
* events identified by EventID instead of string
* `Unsubscribe()` accepts unique subscription ID instead of event name and callback
* no deadlocks anymore: this eventbus doesn't lock mutex while calling external code

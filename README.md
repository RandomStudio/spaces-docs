# Reactive Spaces API

Streams and events should go on separate ports, so that server can optionally subscribe to them.
(A different protocol, e.g. MQTT, could use proper pub/sub behaviour).

## Inputs

### Push Button
* Send
    * "button_push"
        * type: "event"
        * payload
            * none
    * "button_stream"
        * type: "stream"
        * payload
            * newState: boolean (true = pushed)

### Toggle Switch
* Send
    * "switch_toggle"
        * type: "event"
        * payload
            * newState: boolean (true = on, false = off)
    * "switch_stream"
        * type: "stream"
        * payload
            * currentState: boolean (true = on, false = off)
            
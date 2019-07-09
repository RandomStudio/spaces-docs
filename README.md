# Reactive Spaces API

See also [tech notes](./notes.md)

## Simple sensors

Some of the simple sensors listed below could optionally provide either once-off "events" (only when their state changes) *or* a continuous stream of their current state. There needs to be a mechanism of disabling this altogether when not needed, to avoid waste of CPU resources and network bandwidth.

Stream rates should be configurable where a framerate is not typically applicable, e.g. a button state.

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

## Tracking Sensors

### Object 2D Tracker
This data could come from a spinning LIDAR sensor, for example, or a Kinect tracking people in a floor space. Anything that provides a 2D position, 1 or multiple "objects". Objects could be people, vehicles, markers, faces, etc.

In the Kinect example, it is clear that the Kinect hardware could provide at least four different types of data: proximity/presence, 2D tracking, 3D tracking and fully-body (joint) tracking.

* Send
    * "object2d_position"
        * type: "stream"
        * payload
            * `id`: int (a unique identifier for the object in the system)
            * `x`, `y`: floats representing position in whatever units are defined
            * `regionId` (optional; identifier for which ROI the object is inside - send multiple messages if overlapping regions?)
* Configuration
    * "roi" (i.e. "region of interest"; optional; if not sent assume entire tracking region; send multiple with unique IDs to configure multiple ROIs)
        * `id`: int
        * `units`: enum string (what the meaning of the position values is): `mm`, `px`, `grid`
        * `min`:
            * `x`, `y`: floats (position of one corner)
        * `max`:
            * `x`, `y`: floats (position of other corner)
    * "sizeThreshold"
        * `min`
        * `max`
        * `type`: enum string `radius` (default?), `area`, `width`, `height`

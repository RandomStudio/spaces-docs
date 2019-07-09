# Reactive Spaces API

See also [tech notes](./notes.md)

## System overview

An "Interactive Spaces" system will comprise the following parts:

* A single **Controller** (a NodeJS server) plus **Editor** (a frontend application: most of the processing done client-side so server not heavily loaded)
* **Edge Nodes** (NodeJS clients to the controller server). Usually provide a standard way to hook up to hardware, and relay messages via Spaces protocol (e.g. over websocket transport). These would be separate repos, possibly grouped by "category" but in the case of special hardware even by device, e.g.
    * Electronics (via microcontroller), perhaps using Firmata/Johnny Five - includes buttons, sensors, motors, etc.
    * Spinning LIDAR (if not covered by other library)
    * Kinect
    * Video capture (e.g. by ffmpeg)
    * Audio feature extraction (e.g. in combination with [Meyda](https://github.com/meyda/meyda))
    * Live Control device (e.g. MIDI)
* **Shared Modules** (node modules)
    * Type definitions, especially for messaging protocol
    * Configuration and preset loader/saver
    * Utilities

Edge Nodes will be hard-coded to handle the relay (in or out) between the underlying **Interface(s)** and the Controller.

For example, a Kinect might use "Object 2D Tracker", "Object 3D Tracker", "Proximity" and "Body Tracker".

* **Edge Node**, a standalone NodeJS process, which is hard-coded to use one or more
    * **Interface**, which enables (optionally, via runtime configuration) one or more
        * **Senders** which could be either
            * **Events** **...or**
            * Continous **Streams**
        * **Receivers**
        * **Configuration Receivers**

For example, the "Electronics" Edge Node might include all of the Simple Sensor and Motor Control Interfaces, but which of these is actually enabled could be configured at runtime, from the server side.

## Runtime Configuration

On connection to the server, Edge Nodes should publish which **Interfaces** they implement. The Server should be able to enable (subscribe) or enable (unsubscribe) at the **Interface** and **Sender** level, and the Edge Node should prepare to send/receive these types of messages accordingly.

In addition, Interfaces may optionally include standardised `set` type parameters, which controls the hardware or some part of the Edge Node software. These are expected to be used for **live control** or **preset configuration** so that the server can be the overriding "source of truth" for all Edge Node configuration.

## Interfaces

The Interfaces below have been grouped, for convenience, into Interface Groups (though this is not part of the schema as such).

### INPUT: Simple Sensors

Some of the simple sensors listed below could optionally provide either once-off "events" (only when their state changes) *or* a continuous stream of their current state. There needs to be a mechanism of disabling this altogether when not needed, to avoid waste of CPU resources and network bandwidth.

Stream rates should be configurable where a framerate is not typically applicable, e.g. a button state.

For **Event** Senders, it would make sense to provide a standard initialisation event on connection, to get initial state to the controller.

#### Push Button
* Senders
    * "button_push_events"
        * type: "event"
        * payload
            * none
    * "button_push_stream"
        * type: "stream"
        * payload
            * newState: boolean (true = pushed)
* Runtime Configuration
    * `enable_sender button_push_events` true or false
    * `enable_sender button_push_stream` true or false

#### Toggle Switch
* Senders
    * "switch_toggle_events"
        * type: "event"
        * payload
            * newState: boolean (true = on, false = off)
    * "switch_toggle_stream"
        * type: "stream"
        * payload
            * currentState: boolean (true = on, false = off)
* Runtime Configuration
    * `enable_sender switch_toggle_events` true or false
    * `enable_sender switch_toggle_stream` true or false

### INPUT: Tracking

#### Object 2D Tracker
This data could come from a spinning LIDAR sensor, for example, or a Kinect tracking people in a floor space. Anything that provides a 2D position, 1 or multiple "objects". Objects could be people, vehicles, markers, faces, etc.

In the Kinect example, it is clear that the Kinect hardware could provide at least four different types of data: proximity/presence, 2D tracking, 3D tracking and fully-body (joint) tracking.

* Senders
    * "object2d_position"
        * type: "stream"
        * payload
            * `id`: int (a unique identifier for the object in the system)
            * `x`, `y`: floats representing position in whatever units are defined
            * `regionId` (optional; identifier for which ROI the object is inside - send multiple messages if overlapping regions?)
* Receivers
    * *None*
* Runtime Configuration
    * `enable_sender object2d_position` true or false
    * `set_ROI` (i.e. "region of interest"; optional; if not sent assume entire tracking region; send multiple with unique IDs to configure multiple ROIs)
        * `id`: int
        * `units`: enum string (what the meaning of the position values is): `mm`, `px`, `grid`
        * `min`:
            * `x`, `y`: floats (position of one corner)
        * `max`:
            * `x`, `y`: floats (position of other corner)
    * `set_sizeThreshold`
        * `min`
        * `max`
        * `type`: enum string `radius` (default?), `area`, `width`, `height`

#### Object 3D Tracker

#### Body Tracker

### OUTPUT: Lights

Grid / texture based control?

### OUTPUT: Motor control


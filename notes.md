# Notes

## Some suggested tech stack

Test Driven Design: if using Redux, this should be easier to do since most critical operations are done via pure functions.

TypeScript would be a good choice, especially for the backend, to ensure proper validation of data. (Interfaces for complex types, no null values allowed unless explicitly specified.)

[RxJS](https://www.learnrxjs.io/) could be extremely useful for operations on streams of data.

For electronics and micrcontroller interfacing, it might make a lot of sense to use a standard protocol like [Firmata](https://www.arduino.cc/en/Reference/Firmata), perhaps with a JavaScript library such as [Johnny Five](https://github.com/rwaldron/johnny-five) since this already provides standard ways to connect buttons, LEDs, motors, etc.

## Possible code/module organisation scheme

### Repos


Controller + Editor should probably be combined into a single repo, since they logically work as a single system.

All Edge Nodes as separate modules. Add as needed, install only what is needed on hardware for specific projects.

Edge Nodes, in turn, would be hard-coded to implement one or more input/output Interfaces.

Shared Modules could be maintained as separate repos.


# Sensor Discovery for Generic Sensor API

2017-11-07: First draft (initial cases)

The Generic Sensor API spec (https://w3c.github.io/sensors/) and 
derivaties (e.g. https://www.w3.org/TR/orientation-sensor/) provide
a great foundation for unified interaction with device sensors, finally!

However, in my work with empiriKit (a science lab in a box) and with 
hardware to web connectivity work done with industry partners, I strongly
believe that sensor discovery - and even a potential 'Generic Actuator API' - 
would enable application developers to support many more real world scenarios.

With Web USB and Web Bluetooth, it will be possible
to attach/detach hardware and it would be natural to be able to register/deregister 
potential sensors (and actuators) dynamically.

This doc will contain a small collection of cases and snippets as input
for discussion.

## Sensor Discovery

### Case #1: Science lab
Most modern mobile phones contain several movement sensors of varying quality.
Connecting a range of e.g. high precision, high frequency accelerometers 
via Web USB allows for low cost, industry grade readings.  Allowing those
to be registered and discovered within the Generic Sensor context makes it
possible for vendors to create 'web drivers', enabling easy integration
for application developers, using the Generic Sensor API alone.

### Case #2: Vehicle monitoring
When connecting my phone to my car (or bike) via BLE or USB, I'd like for 
any web app utilizing sensor data (geolocation, ambient light, temperature, etc.)
to be able to tap into the extra sensors available.

It should be possible to register and prioritize (e.g. 'new default')
these sensors to be discoverable as first class Generic Sensors for the app.

### Case #3: Local temperature, pressure, gas
(related to https://webbluetoothcg.github.io/web-bluetooth/scanning.html)
As a quality inspector, I'd want to be able to walk through a storage facility
(or supermarket) and have my web application dynamically pick up equipment
sensor beacon data and dynamically register/deregister sensors as I move.

### Case #4: Home automation
Given that BLE Mesh implementations for home automation is moving now it would 
only be natural if a web app connecting to a node in the house would be able to 
discover and interact with the sensors and actuators available.  Allowing 
dynamic registration/deregistration in the Generic Sensor (and Actuator) context
will provide app developers a generalized and portable approach to interacting
with home automation units (thermostats, lights, environmental readings, etc.).

## Snippets

### Example #1: Listing

In general, sensors connected via Web USB or Web Bluetooth have already gone 
through a user gesture driven permission approval flow.  With that in mind,
one could argue that sensors connected via these mechanisms and added by the web
application, should not require a subsequent permission granting dialog.
This would include listing of available sensors, which could return a
merge of pre-approved built-in generic sensors and sensors added by the web
application.

```javascript
const accelArray = await navigator.sensors.getSensors({filters:[{type:"accelerometer"}]);
```

### Example #2: Registration

// UNDER INVESTIGATION

TODO: Look into how e.g. Streams API can be used to provide high-bandwidth support
for e.g. high speed accelerometers in parallel with CustomEvent support for 
low-bandwidth sensors (geolocation, temperature, etc.).

(TBD: can be multi-purpose/combo)

```javascript
class MyWebUSBAccelSensor extends Accelerometer {

    constructor(options) {
        // e.g. connect to Web USB 
    }
    ...

    static get capabilities() {
        return {
            frequency: {
                min: 1,
                max: 100,
                default: 10
            },
            ...
        }
    }

    _onDataFromMySensor(in) {
        ...
        this.onreading(out)
    }

    _onError(reason) {
        ...
        this.onerror(err)
    }

    ...
}
navigator.sensors.register(MyAccelSensor)
```

### Example #3: Connect/disconnect

Pre-approved sensors (or sensor types, possibly), when registered and unregistered/disconnected.

```javascript
navigator.sensors.addEventListener('connect', evt => handleConnectSensor);
navigator.sensors.addEventListener('disconnect', evt => handleDisconnectSensor);
```

## Generic Actuator
Sensors only cover half of the interaction with the physical world.  Often
there will be a need to control actuators (e.g. a thermostat, motor or other).
This can either be a manual action or as part of an automatic response to
sensor readings and it would be natural to have a Generic Actuator API to 
complement the Generic Sensor API to create a uniform approch for interacting
with the physical world via a web application. 

## ServiceWorker integration?
In scenarios, where the user would like to get notified on significant
changes to sensor readings, e.g. 'temperature dropped below X' or 'someone 
opened my door (BLE mesh accelerometer)', it would (?) require a mechanism for
the service worker to give a notification and potentially wake up the monitoring
web application.



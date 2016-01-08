# OpenTrons Raspberry Pi Bootstrapper

This repo doesn't contain any of the actual application code, but it is in charge of taking a fresh Ubuntu instance and turning it into an OpenTrons interface that can plug into a CNC controller board (currently [Smoothieboard](http://smoothieware.org/smoothieboard)) in order to issue instructions and move the OpenTrons robot around.

The OpenTrons machine uses a [Raspberry Pi](https://www.raspberrypi.org/) mini computer to run the robot interface, which is served as a locally hosted web service.

This can be accessed either directly by plugging a keyboard and mouse into the USB ports on the back of the Pi, or by creating a network bridge through the Ethernet port ([Windows](https://github.com/Opentrons/otone_docs/blob/master/Setup_Windows.md#configure-network-settings), [Mac](https://github.com/Opentrons/otone_docs/blob/master/Setup_Mac.md#1-enter-system-preferences)) and connecting via a web browser on a client laptop.

## Docker Images

The interface is divided into several "mini" applications, meant to be modular.

You can find them in their respective repositories, listed below:

- [pi-frontend](https://github.com/Opentrons/pi-frontend)
- [pi-labware](https://github.com/Opentrons/pi-labware)
- [pi-driver](https://github.com/Opentrons/pi-driver)

### Frontend

The frontend application runs a webserver which acts as the graphical user interface for the OpenTrons machine.

The code is written on top of Flask/React and talks to the labware backend.

### Labware Backend

The "smarts" of the robot, this interfaces between the frontend and the robot control module in order to provide high-level data concerning the robot state to the frontend or motor
control instructions to the robot controller.

It should be theoretically possible to connect to this directly to execute Python code and specified commands without using the graphical interface, allowing for scripted and remote
 operation, or for user interfaces to created for alternative platforms/needs.

### Robot Controller

This takes coordinates and other instructions and translates them to language understood by the motor controller (in our case, a Smoothieboard).  This can be swapped out for other s
ystems, provided it responds to the same interface.

## Persistent File Locations

All the Docker containers (which hold the actual application software) revert to their initial build state (the container file that we build).  Which means they have no persistent d
ata on their own.  Any persistency will be with a volume share to the /config directory.

This solves the problem of the previous robot builds, in which it's possible to put the robot into a broken state which can't be repaired by following the normal installation proced
ure.  In this new system, we can theoretically have the user delete their /config directory and restart the installation process from the first step.

### /builds

Docker images of previous/current builds, used for rolling back and forward.

### /data

Robot output, logged runs, uploaded protocols, saved images, etc.

Nothing in this directory should be part of the robot's operating state.

Protocols obviously can be reloaded to dictate what the robot runs, but that differs from configuration because it's user input.

### /config

User configuration.

These are all the little variables that make each individual robot unique and special, and also so fun to debug.

This should be easy to output through a debug mechanism so that users can upload their configuration.
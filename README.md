mqtt-gpio-monitor
=================

Python script for sending/receiving commands to/from GPIO pins via MQTT messages.

This was written for use on a RaspberryPi, with either the PiFace extension board, or just raw access to the GPIO pins. 

The example INI file contains the only configuration required. You must define the module to use, either GPIO or PFIO. Depending on which you will need to ensure the appropriate Python module is installed.

If using the PiFace extension board you will need to follow the instructions [here](http://piface.github.io/pifacedigitalio/installation.html) to install the digital IO libraries;

    sudo apt-get install python-pifacedigitalio

If just using the raw GPIO pins then the RPi.GPIO module should be installed as part of Raspbian.

You will also need an MQTT client, in this case [Paho](https://pypi.python.org/pypi/paho-mqtt/0.9). To install;

    sudo pip install paho-mqtt

You should now be ready to run the script. It will listen for incoming messages on {topic}/in/+ (where {topic} is specified in the INI file). The incoming messages need to arrive on {topic}/in/{pin} with a value of either 1 or 0. Alternativly, you can supply a sequence of pin value changes with pauses between them. The payload form is "0|1 delay 0|1 delay ..." e.g. "1 1000 0" would set the pin high, wait 1000ms then set it low.

The script also supports setting up PWM on GPIO pins. To do this use the payload form "pwm frequency duty_cycle" e.g. "pwm 100 50"

E.g. a message arriving on {topic}/in/3 with value 1 will set pin 3 to HIGH. 

Depending on what is set for MONITOR_PINS in the INI file, the script will also monitor these pins and if there are any changes publish a message on {topic}/out/{pin} with a value of either 1 or 0.

E.g. if pin 7 changes from 1 to 0 a message would be published to {topic}/out/7 with a value of 1.

So you are able to both monitor pins as well as set pins HIGH/LOW. Obviously you can't do both monitor and update for the same pin.

The last option in the INI file is MONITOR_REFRESH, which is a special topic the script will subscribe to if specified. Any message arriving on that topic will trigger the script to re-send publishes with the current state of all monitored pins. This is useful for requesting the current state of all pins if the calling system is restarted for example.

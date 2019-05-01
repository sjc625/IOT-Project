# IOT-Project
Open Source Bluetooth music controller!

The plan for this project was to use multiple USB bluetooth dongles to connect
multiple speakers to one pi. This plan failed, as the pi cannot interpret multiple
bluetooth streams, at least not without heavy modification.

The solution was to use the onboard bluetooth and connect speakers via the audio jack.

Link to slideshow:
https://docs.google.com/presentation/d/1We8Kkka198JY1uNB_UCtpetBcNZHRT4wdZDhmuxaNaw/edit?usp=sharing

Compatibility - Raspbian (as of 5/1/19)
In order to get the system started-

Download this repo

git clone https://github.com/sjc625/IOT-project

Follow the instructions at this link

https://raspberrypi.stackexchange.com/questions/47708/setup-raspberry-pi-3-as-bluetooth-speaker/48055#48055

Also outlined here

Get required libraries

sudo apt-get update && sudo apt-get install bluez pulseaudio-module-bluetooth python-gobject python-gobject-2 bluez-tools udev

set user as a mod

sudo usermod -a -G lp pi  #pi is your username

create new config under /etc/bluetooth/audio.conf using text editor and add the following line

[General]:
Enable=Source,Sink,Media,Socket
edit file /etc/bluetooth/main.confusing your preferred text editor (I'm using nano).

Set Bluetooth Class, Modify the following line to:

 Class = 0x00041C
0x000041C means that the rpi bluetooth support A2DP protocol.

change /etc/pulse/daemon.conf add / modify (don't forget to check the code thoroughly before adding them), and change

resample-method = trivial
you can using any method you like, I used speex-float-5


I have been on this project after while (to help my friend doing their paper for graduating) and find the online project doing just fine (although the pi processing the audio is quite lagging the pi, and voltage drop make it freeze as the only way to make it reboot is to unplugged the power cable).

This is the step that I've been working on and it works on raspberry pi 3.

1. Download the required package
This project depend on pulseaudio so grab it and installing by typing:

sudo apt-get update && sudo apt-get install bluez pulseaudio-module-bluetooth python-gobject python-gobject-2 bluez-tools udev
i rather update the firmware of raspberry first before installing them because i have problem with rpi-bluetooth package so i do:

sudo rpi-update
and make it install and advance to next step.

2. Edit Configuration and applied it
First add pi username to the group pulseaudio with

sudo usermod -a -G lp pi
create new config under /etc/bluetooth/audio.conf using text editor and add the following line

[General]:
Enable=Source,Sink,Media,Socket
edit file /etc/bluetooth/main.confusing your preferred text editor (I'm using nano).

Set Bluetooth Class, Modify the following line to:

 Class = 0x00041C
0x000041C means that the rpi bluetooth support A2DP protocol.

change /etc/pulse/daemon.conf add / modify (don't forget to check the code thoroughly before adding them), and change

resample-method = trivial
you can using any method you like, i personally using speex-float-3 for reference you can see this link

start pulseaudio service with:

pulseaudio -D
we are going to use ragusa87 script to automate the bluetooth source to audio sink. First please add new configuration to udev init.d by editing file /etc/udev/rules.d/99-input.rules and add this to the file

SUBSYSTEM="input", GROUP="input", MODE="0660"
KERNEL=="input[0-9]* ", RUN+="/usr/lib/udev/bluetooth"
add folder udev to /usr/lib by using mkdir

sudo mkdir /usr/lib/udev && cd /usr/lib/udev
and move the bluetooth file from this repo to /usr/lib/udev (credits ragusa87)

PLEASE NOTE that your AUDIOSINK might different from mine, check it before using pactl list short sinks
Change all paths in puslcript volup and voldwn to the appropriate paths.
vol.txt is just the current volume setting.

Then, connect your device to the bluetooth using the GUI in the top right (Raspbian)
This will produce an error. Don't worry about it.

Run the pulscript script. This sets audio configuration.

Connect the phone again, either through the devices bluetooth or the pi. This
will not produce an error, as now pulseaudio has rerouted the device as an audio input.

play music as normally, using volup and voldwn to change volume!!

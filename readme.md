Firebolt SDK version 0.3
===================
- [Introduction](#intro)
- [Prerequisites](#prerequisites)
    - [System Requirements](#system-requirements)
    - [Software Requirements](#software-requirements)
- [Installation](#installation)
    - [Installation of RDK Image on Raspberry Pi](#installation-of-rdk-image-on-raspberry-pi)
    - [Installation of Firebolt SDK](#installation-of-rne-sdk)
- [Developing applications](#developing-applications)
    - [Troubleshooting](#troubleshooting)
    - [Breakpad support](#breakpad-support)
    - [Bluetooth support](#bluetooth-support)
    - [GDB Support](#gdb-support)
    - [Generating Corefiles](#generating-corefiles)

- [Terminal Access](#terminal-access)
	- [For Raspberry Pi](#for-raspberry-pi)
	- [For Comcast Devices](#for-comcast-devices)
- [Installing Sample Applications](#installing-sample-applications)
    - [Preparing USB](#preparing-USB)
	- [Running Sample Applications on Raspberry Pi](#running-sample-applications-raspberry-pi)
	- [Running Sample Applications on Comcast Devices](#running-sample-applications-comcast-devices)
	- [App Manager Registry File](#app-manager-registry-file)
	- [Running Sample Applications Standalone on Raspberry Pi](#running-sample-applications-standalone-on-raspberry-pi)
	- [Running Sample Applications Standalone on Comcast Devices](#running-sample-applications-standalone-on-comcast-devices)
- [Using the App Manager](#using-the-app-manager)
	- [App Manager Logging on Raspberry Pi](#app-manager-logging-raspberry-pi)
	- [App Manager Logging on Comcast Devices](#app-manager-logging-comcast-devices)
- [Key App Features](#key-app-features)
    - [Graphics With Wayland](#graphics-with-wayland)
    - [Keyboard And Remote Input](#keyboard-and-remote-input)
    - [Handling Resolution Changes](#handling-resolution-changes)
    - [Communication With App Manager](#communication-with-app-manager)
    - [Suspend And Resume](#suspend-and-resume)
    - [Audio And Video Playback With Gstreamer](#audio-and-video-playback-with-gstreamer)
- [Sample Applications](#sample-applications)
    - [Graphics Sample](#graphics-sample)
    - [Player Sample](#player-sample)
    - [Lifecyle Sample](#lifecycle-sample)
    - [MSE Player Sample](#mse-player-sample)
    - [Lightning cast Sample](#lightning-cast-sample)

<a name="intro"></a>

Introduction
------------
RDK Native Environment SDK (Firebolt) is intended to provide a development environment for applications targeted to run in RDK environment.

<a name="prerequisites"></a>

Prerequisites
-------------
<a name="system-requirements"></a>
### System Requirements ###

Ubuntu 14.4 **64 bit** OS or higher
8GB of RAM

<a name="software-requirements"></a>
### Software Requirements ###

The following software packages needs to be installed
- awk
- wget
- git-core
- diffstat
- unzip
- texinfo
- gcc-multilib
- build-essential
- chrpath
- socat
- cpio
- python
- python3
- python3-pip
- python3-pexpect
- xz-utils
- debianutils
- iputils-ping
- libsdl1.2-dev
- xterm

This can be installed using the following command

     sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib \
	 build-essential chrpath socat cpio python python3 python3-pip python3-pexpect \
	 xz-utils debianutils iputils-ping libsdl1.2-dev xterm

<a name="installation"></a>

Installation
------------

<a name="installation-of-rdk-image-on-raspberry-pi"></a>
### Installation of RDK Image on Raspberry Pi ###

Firebolt support the following Raspberry Pi models
- Raspberry Pi 3 Model B+
- Raspberry Pi 3 Model A+
- Raspberry Pi 3 Model B
- Raspberry Pi 2 Model B
- Raspberry Pi Zero W*

*Raspberry Pi Zero W SDK/image is not compatible with other models in this version.

As part of Firebolt package you should receive a compressed rdk sdimg.xz to flash to the raspberry pi sdcard.
Insert the raspberry pi sdcard into your desktop machine and flash the image using the
following commands (**This will overwrite anything on the sdcard**).

>       sudo mkfs.ext3 /dev/sdX
>       xzcat /path/to/image/*.rpi-sdimg.xz  | sudo dd of=/dev/sdX bs=4M
>       sync

Replace */path/to/image/*  with correct path to sdimg.xz.
Replace the *X* in *sdX* with the correct device letter of the sdcard when inserted into your machine.
**Example:**
>       xzcat RPIMC_default_20181015101838sdy.rootfs.rpi-sdimg.xz  | sudo dd of=/dev/sdc bs=4M


Once flashed insert the sdcard into the pi and make sure it has a valid ethernet and HDMI connection.
Then power up the pi, it should boot up into the app manager screen with the ethernet IP listed at the bottom right.

<a name="installation-of-rne-sdk"></a>
### Installation of Firebolt SDK ###

To install the sdk, go the directory where it is copied, and run the following commands:

On Raspberry Pi:
>      chmod +x ./raspberrypi-rdk-mc-RNE-SDK-2.0.sh
>      ./raspberrypi-rdk-mc-RNE-SDK-2.0.sh

On Comcast Devices:
>      chmod +x ./DEVICE-RNE-SDK-2.0.sh
>      ./DEVICE-RNE-SDK-2.0.sh
Replace DEVICE with your particular device: e.g. (pacexi5, pacexi5-RNE-SDK-2.0.sh)

Follow the instructions in the screen regarding installation directory. If you don't want to change the default directory, please press *ENTER* and proceed.
It's easiest to run the above script in an empty directory and type . to install in the current directory.

<a name="developing-applications"></a>

Developing applications
------------

Assuming that the SDK is installed in *sdk_root* directory, issue the following command

>      source  <sdk_root>/environment-setup-<chipset>-rdk-linux-gnueabi

**Example:**
>      source RNE-3.0/environment-setup-cortexa7t2hf-vfp-vfpv4-neon-rdk-linux-gnueabi

Here RNE-3.0 is sdk_root directory

Now all the cross compiler tools are available for development. Four native sample applications are provided as part of build. To build the samples, go to the samples directory and execute the following command

>      ./build_samples.sh

After the samples are built they are contained in a partnerapps directory for copying to the provided device.

<a name="breakpad-support"></a>
### Breadpad support ###
Google breakpad support is available for the applications. For more information about Google breakpad and it's integration, check this link
https://chromium.googlesource.com/breakpad/breakpad/+/master/docs/linux_starter_guide.md
The static library is added as part of SDK.

The rne player application, included as part of this, has breakpad support.

<a name="bluetooth-support"></a>
### Bluetooth support ###
This version supports bluez version 5.45. The support is limited to Human Interface Devices (HID) in this version.

<a name="troubleshooting"></a>
### Troubleshooting ###
If a shared library has secondary dependencies which it cannot resolve, the following linker flags should be added to tell the linker to ignore its undefined symbols
-Wl, --allow-shlib-undefined

<a name="gdb-support"></a>
### GDB Support ###
Currently GDB is not shipped with the firemware/SDK.  It can be built with the sdk and copied to the device.
Shell/SSH access is necessary to use it.

1.  Download the source for gdb.
2.  Make sure you are in a shell setup for Firebolt Development
3.  Create a directory for installing gdb
>      mkdir gdb_install

4.  Enter the directory of the downloaded gdb source, CONFIGURE_FLAGS is already set in a Firebolt shell for cross compiling needs
>      cd gdb-x.x
>      ./configure $CONFIGURE_FLAGS --prefix=/path/to/gdb_install
>      make
>      make install

5.  At the root of your usb key, create a usr directory
>      cd /path_to_root_of_usb_key
>      mkdir usr
6.  Copy contents of gdb_install directory into usr on your usb key
>      cp -r gdb_install/*  /path_to_root_of_usb_key/usr/
7.  Insert usb key into device
8.  Edit run_partner_app.sh script with the following:
>       export LD_LIBRARY_PATH=/usb/usr/lib

    Add the above to the script so gdb libs can be found

    Change the launch line to prefix with the path to gdb on the key
>       /usb/usr/bin/gdb /usb/partnerapps/rne-triangle/rne_triangle

This launch script provided with the Firebolt SDK can be modified to launch any binary built
with and without gdb.  The launch script should be at the root of the partnerapps folder.

**Caveat**

Somtimes compiled library dependencies can exist in the Firebolt sdk but not on the firmware.  For example when running GDB it may say its missing
libncursesw.so.5.  This can be resolved by copying the needed Firebolt SDK library onto our new lib directory on the key:
>     cp /path_to_sdk/sysroots/cortexa15t2hf-neon-rdk-linux-gnueabi/lib/libncurses.so.5 /path_to_root_of_usb_key/usr/lib/


<a name="generating-corefiles"></a>
### Generating Corefiles ###
By default on many Comcast devices core dump support is turned off for space reasons.  We can override this though, for Firebolt development purposes.
Shell/SSH access is necessary for this.

1. Insert the usb key into the device
2. Create a cores directory on the key
>       cd /usb
>       mkdir cores
3. Enable coredumps
>       ulimit -c unlimited
4. Change /proc/sys/kernel/ to write cores to the usb key
>       echo "/usb/cores/%e_%s_%t" > /proc/sys/kernel/core_pattern

Corefiles can then be evaluated on the device by using gdb built for the device or can be evaulated
using a gdb built on/for the ubuntu desktop with multiarch support.



<a name="terminal-access"></a>

Terminal Access
------------

<a name="for-raspberry-pi"></a>
### For Raspberry Pi ###
Once the pi is booted into the app manager and the pi has a valid ethernet connection the app manager will list the device's ethernet ip on the bottom right.
This can be used to ssh into the box from a machine on the same network.  Login is root, password is empty.

Example:
>      ssh root@192.168.0.103

Then press enter twice to get ssh terminal access to the box.

<a name="for-comcast-devices"></a>
### For Comcast Devices ###
SSH access instructions will be provided to you.

<a name="installing-sample-applications"></a>

Installing Sample Applications
----------------------
The following sections will detail how to load and run the provided sample applications on the provided device.

<a name="preparing-USB"></a>
### Preparing USB ###
As of this release, USB storage device should have **single partition and ext3 format**.
To format USB in ext3

**Below assumes usb device is on device sdb**

- Unmount USB drive
>      sudo umount /dev/sdb

- Format USB drive in ext3 format
>      sudo mkfs.ext3 /dev/sdb

<a name="running-sample-applications-raspberry-pi"></a>
### Running Sample Applications on Raspberry Pi ###

Once the sample applications are built with the provided sdk a partnerapps folder will be created at the root of the samples directory.
You can then place this directory at the root of the USB storage device and then insert the USB stick into the pi.
Once inserted, use a USB keyboard and press ctrl-e to reload the app manager. You will now see all the sample apps in the list.

You can also have the app manager launch your own binary by renaming the binary *partnerapp* and placing it in the partnerapps folder at the root of the USB storage device.

<a name="running-sample-applications-comcast-devices"></a>
### Running Sample Applications on Comcast Devices ###
Once the sample applications are built with the provided sdk a partnerapps folder will be created at the root of the samples directory.
You can then place this directory at the root of the USB storage device and then insert the USB stick into the comcast device.

If provided ssh access:

1.  SSH into the comcast device
2.  Mount the usb key to /usb

>     mount /dev/sda /usb

Once inserted, use a USB keyboard and press ctrl-e to reload the app manager. You will now see all the sample apps in the list.

You can also have the app manager launch your own binary by renaming the binary *partnerapp* and placing it in the  partnerapps folder at the root of the USB storage device.


<a name="app-manager-registry-file"></a>
### App Manager Registry File ###
The appmanagerregistry.conf is a simple json file that the app manager reads to find the apps it can launch.
This file is contained at the root of the partnerapps directory on the USB storage device.
It can easily be modified to launch and interact with new partner applications.

<a name="running-sample-applications-standalone-on-raspberry-pi"></a>
### Running Sample Applications Standalone on Raspberry Pi ###
For debugging purposes you can run your applications standalone without the app manager
Once sshed into the raspberry pi do the following:

- Shutdown the app manager
>      systemctl stop appmanager
- Start the westeros service (Needed for graphics to be displayed)
>      systemctl start westeros
- Run the provided run_partner_app.sh script provided in the partnerapps folder
>      /usb/partnerapps/run_partner_app.sh

By default the the above shell script runs the rne_triangle sample app.  But it
can easily be modified to run any app of your choosing by editing the last line
in the script.

To use the app manager again, just start its service back up.
>      systemctl start appmanager

<a name="running-sample-applications-standalone-on-comcast-devices"></a>
### Running Sample Applications Standalone on Comcast Devices ###
For debugging purposes you can run your applications standalone without the app manager
provided you have ssh access into the comcast device.
Once sshed into the comcast device do the following:

- Shutdown the app manager
>      systemctl stop xre-receiver
- Start westeros (Needed for graphics to be displayed, this only needs to be run one time)
>      /usb/partnerapps/run_westeros.sh
- Run the provided run_partner_app.sh script provided in the partnerapps folder
>      /usb/partnerapps/run_partner_app.sh

By default the the above shell script runs the rne_triangle sample app.  But it
can easily be modified to run any app of your choosing by editing the last line
in the script.

To use the app manager again, just start its service back up.
>      systemctl start xre-receiver


<a name="using-the-app-manager"></a>

Using the App Manager
----------------------

A USB keyboard or remote control (on supported systems) or mouse can be used on the device to interact with the app manager.

The following actions are supported:

The UI supports enabling and disabling suspend mode option in the UI.
When Suspend mode is enabled, it provides suspend/resume options for Spark and Native apps.
For Web apps Suspend mode is not supported.

To enable/disable suspend mode, navigate using keyboard or remote or mouse to the "Options" on top the top right corner of UI.

### To see menu on the "Options"
  - on keyboard, press Enter key
  - on mouse, Left click
  - on remote, press OK button
### To enable/disable Suspend mode
  - Open "Options" on the top right corner
  - Navigate to enable/disable Suspend mode. To enable/disable,
    - Press Enter on keyboard
    - Press OK on remote
    - Left click on mouse

### To navigate through the apps,
  - on keyboard, press the  up, down, left or right arrow keys
  - on remote, press the arrow keys
  - When Mouse is connected, mouse can be hovered to navigate through the apps 
### To launch an app,
  - on keyboard, Press Enter key
  - on remote, Press OK button
  - on mouse, Left click on Mouse
  - When Suspend mode is enabled, select "Start" to launch the app.
### To suspend/resume an app in suspend mode,
  - Navigate to the app and open menu options
  - Select suspend/resume option using keyboard/mouse/remote.
### When an application is taking up the whole screen to go back to the app manager menu,
  - on keyboard, press Ctrl-m
  - on remote, press xfinity key
  - When Suspend mode is disabled, this will stop the app and show app manager menu
  - When Suspend mode is enabled,
    - This will run the app in background and show app manager menu
    - To Stop the app, select "Stop" from menu options
### To reload the app manager (if the contents of the usb key were changed),
  - on keyboard, press Ctrl-e
  - on remote, press Exit key

<a name="app-manager-logging-raspberry-pi"></a>
### App Manager Logging On The Raspberry Pi ###
The app manager application will log all its output to /opt/logs/appmanager.log.

All partner applications that are launched from the app manager will have their stdout/stderr also redirected to this file.

<a name="app-manager-logging-comcast-devices"></a>
### App Manager Logging On Comcast Devices ###
The app manager application will log all its output to /opt/logs/receiver.log.

All partner applications that are launched from the app manager will have their stdout/stderr also redirected to this file.

<a name="key-app-features"></a>

Key App Features
------------

<a name="graphics-with-wayland"></a>
### Graphics With Wayland ###

The app manager has the job of running a wayland compositor for all the apps it runs.  Before painting to the screen apps
will connect to this compositor using wayland calls.

The following is a general graphics setup flow.
```
// Connect to compositor
display= wl_display_connect(display_name);  // A NULL display name connects to default WAYLAND_DISPLAY set by app manager

// Use the registry as the hook to connect to the rest of the wayland callbacks
registry= wl_display_get_registry(display);
wl_registry_add_listener(registry, &registryListener, &ctx);

// Complete communication to compositor for above commands
wl_display_roundtrip(display);

// Setup EGL

// Use the the wayland display to get the egl display
ctx->eglDisplay = eglGetDisplay((NativeDisplayType)display);
// Continue setting up EGL...

// Create a surface with the compositor
// Get compositor reference from wayland registry callback
// Create surface
ctx->surface= wl_compositor_create_surface(ctx->compositor);

// Create egl window from surface
ctx->native= wl_egl_window_create(ctx->surface, ctx->planeWidth, ctx->planeHeight);

// Continue setting up surface with normal egl calls..

// Do Open GL setup for your app

// Run graphics app loop
while (shouldRunMainLoop)
{
  // dispatch queued wayland events on default queue, can also use blocking method wl_display_dispatch
  wl_display_dispatch_pending(display)

  // render your gl scene

  // swap buffers
  eglSwapBuffers(eglDisplay, eglSurfaceWindow);
}
```

<a name="keyboard-and-remote-input"></a>
### Keyboard And Remote Input ###

All keyboard and remote control (for supported systems) for a native app is handled through the standard wayland seat mechanism.
Remote control keys will come in as a standard wayland keyboard seat.  The app manager compositor will route input to the focused
app.

The following is a general way of getting keys
```
// Connect to display and registry callback with wayland registry
display= wl_display_connect(display_name);
registry= wl_display_get_registry(display);
wl_registry_add_listener(registry, &registryListener, &ctx);

// In registry callback add a seat listener
seat= (struct wl_seat*)wl_registry_bind(registry, id, &wl_seat_interface, 4);
wl_seat_add_listener(seat, &seatListener, ctx);

// In seat listener add listener for keyboard
keyboard= wl_seat_get_keyboard( seat );
wl_keyboard_add_listener( keyboard, &keyboardListener, ctx );

// Use the keyboard listener callbacks to get key information:
static const struct wl_keyboard_listener keyboardListener = {
  keyboardHandleKeymap,
  keyboardHandleEnter,
  keyboardHandleLeave,
  keyboardHandleKey,
  keyboardHandleModifiers,
  keyboardHandleRepeatInfo,
};
```

<a name="handling-resolution-changes"></a>
### Handling Resolution Changes ###

Resolution changes can be handled through the wayland output mechanism.
Add a listener on wl_output and then a callback for outputHandleMode.
The outputHandleMode callback will be called with resolution changes.
This will also be called once first registered to get initial output resolution.
```
// Connect to display and registry callback with wayland registry
display= wl_display_connect(display_name);
registry= wl_display_get_registry(display);
wl_registry_add_listener(registry, &registryListener, &ctx);

// In registry callback add an output listener
output= (struct wl_output*)wl_registry_bind(registry, id, &wl_output_interface, 2);
wl_output_add_listener(output, &outputListener, ctx);

// Make sure your outputListener contains an outputMode callback
static const struct wl_output_listener outputListener = {
   outputGeometry,
   outputMode,
   outputDone,
   outputScale
};

//output mode will get passed in a width and height
static void outputMode( void *data, struct wl_output *output, uint32_t flags,
                        int32_t width, int32_t height, int32_t refresh )
{
}
```

<a name="communication-with-app-manager"></a>
### Communication With App Manager ###

Apps will communicate with the app manager through the interprocess communication library rt.
rt enables the app manager to call functions in your application and for your application
to send events and status to the app manager.

Using rt:
```
// Your object to be registered must inherit the rtObject interface
// Here you will declare your object and define functions.
class MyObject : public rtObject {
 public:
  rtDeclareObject(MyObject, rtObject);
  rtMethodNoArgAndNoReturn("suspend", suspend);
  rtMethodNoArgAndNoReturn("resume", resume);

// You then need to initialize rt and register your object.
rtError rc = rtRemoteInit();
const char* objectName = "MY OBJECT";
MyObject* myObject = new MyObject;
rc = rtRemoteRegisterObject(objectName, myObject);

// Then for rt processing to work you must call its process
// iteration in your apps main loop.

// This will register a callback that will get called everytime there is rt messages
// to be processed
rtRemoteRegisterQueueReadyHandler( rtEnvironmentGetGlobal(), rtRemoteCallback, nullptr );

// You then must call rtRemoteProcessSingleItem() to process the message. You can do
// this as part of your apps main loop or use a thread and wait on a condition.
```

<a name="suspend-and-resume"></a>
### Suspend And Resume ###

Apps should support a suspend and resume option if needed so they can start quickly
and stay running in the background.  Apps when suspended should free all graphics
and AV resources while using minimal CPU and RAM while running in the background.
When the app is resumed it should be usable by the user in a few seconds.

The app manager is ready to call suspend/resume on your app through rt.
You will need to initialize and register an object to rt that contains
these functions.
```
// Your object to be registered must inherit the rtObject interface
// Here you will declare your object and define functions.
// Header file:
class MyObject : public rtObject {
 public:
  rtDeclareObject(MyObject, rtObject);
  rtMethodNoArgAndNoReturn("suspend", suspend); //declare suspend function to rt
  rtMethodNoArgAndNoReturn("resume", resume); //declare resume function to rt

  rtError suspend(); //actual suspend c++ function
  rtError resume(); //acutal resume c++ function

//CPP file:
// Define rt object and methods
rtDefineObject (MyObject, rtObject);
rtDefineMethod (MyObject, suspend);
rtDefineMethod (MyObject, resume);

// Implement suspend/resume c++ functions
rtError MyObject::suspend()
{
  // suspend your app from active state
}

rtError MyObject::resume()
{
  // resume your app from suspend state
}
```

<a name="audio-and-video-playback-with-gstreamer"></a>
### Audio And Video Playback With Gstreamer ###

How your app generally uses gstreamer is up to you.  Ideally your app will be able to use a playbin pipeline.
The only requirement is that your app must use gstreamer for its audio and video needs and that it
use the westerossink for its videosink.  westerossink allows your video path to go through wayland
so it can be manipulated by the application manager if necessary.  A simple gstreamer use case
is showcased in the rne-player sample app and a more real world MSE type AV player is showcased in the
mse-player sample application.

<a name="sample-applications"></a>

Sample Applications
------------
The provided sample apps are to help developers get up to speed with using the graphics and video APIs in Firebolt.
Generally all graphics and keyboard/remote input will be provided through wayland, and audio and video will use gstreamer.

<a name="graphics-sample"></a>
### Graphics Sample ###
rne-triangle sample app demonstrates a simple graphics app that uses opengl es and wayland to render a spinning triangle to the screen.
The following key features are demostrated:

1.  How to use wayland and OpenGL ES to render graphics
2.  How to get keyboard/remote input
3.  How to get screen resolution

<a name="player-sample"></a>
### Player Sample ###
rne-player sample app shows how to build and use a simple gstreamer pipeline that uses westerosink.  It will load a movie from a URL,
buffer it, and play it back.
The following key features are demonstrated:

1.  How to build a simple gstreamer pipeline with westerossink
2.  How to get screen resolution

<a name="lifecycle-sample"></a>
### Lifecycle Sample ###
graphics-lifecycle sample app extends the rne-triangle sample app to support rt and suspend/resume.
The following key features are demonstrated:

1.  How to use wayland and OpenGL ES to render graphics
2.  How to get keyboard/remote input
3.  How to get screen resolution
4.  How to setup and register an object with rt
5.  How to handle suspend/resume

<a name="mse-player-sample"></a>
### MSE Player Sample ###
The MSE (Media Source Extensions) player sample app demos how to put everything together for a more real world example.
The app will show how to build a gstreamer pipeline that can be fed raw H264 and AAC frames asynchronously. The sample content
contains three separate fragments of the same video to show how to simulate a seek by flushing the video pipeline and providing
new samples at different period in time.  The app also uses the essos library which simplifies setting up wayland for graphics
and keyboard/remote input.

The following key features are demonstrated:

1.  How to setup and use a more complicated gstreamer pipeline with a custom source
2.  How to setup and register an object with rt
3.  How to handle suspend/resume

<a name="lightning-cast-sample"></a>
### Lightning cast Sample ###
This sample app demonstrates how a native application can trigger the launch of another application via rt-remote mechanism. It
also demonstrates how to connect to a service dynamically by ping and bye methods. The sample app is already part of the image.
The source is avaiable in samples/rtcast folder.

To run this app, go the shell and type the following command
> root@arrisxi6:~# LighteningCast

The app will present the following options
```
root@arrisxi6:~# LighteningCast
LighteningCastService!!!  Func: main
rtLighteningCastRemoteObject(): Service com.comcast.lighteningcast
LIGHTENINGCAST>>>>
Select Options:
1) launch Application
2) stop Application
3) query Application status
4) Exit
```

Choose 1 to launch the application . The names listed in the appmanagerregistry.conf can be used to invoke the launch.
```
root@arrisxi6:~# LighteningCast
LighteningCastService!!!  Func: main
rtLighteningCastRemoteObject(): Service com.comcast.lighteningcast
LIGHTENINGCAST>>>>
Select Options:
1) launch Application
2) stop Application
3) query Application status
4) Exit
rt: WARN rtRemoteServer.cpp:793 -- Thread-31930: failed to get property: 8907a0a6-ef86-4c3d-aea1-c40c0aa2f6f0. RT_PROP_NOT_FOUND
1

Launch Application
Application Name :

```
Provide the name of the application at this point
```
Select Options:
1) launch Application
2) stop Application
3) query Application status
4) Exit
1

Launch Application
Application Name : VOD-Spark
Launch Params:

```
Press Enter since there are no arguments to be passed.
```
rt: WARN rtRemoteServer.cpp:793 -- Thread-31926: failed to get property: 8907a0a6-ef86-4c3d-aea1-c40c0aa2f6f0. RT_PROP_NOT_FOUND
Select Options:
1) launch Application
2) stop Application
3) query Application status
4) Exit
1

Launch Application
Application Name : VOD-Spark
Launch Params:
rtLighteningCastRemoteObject::launchApplication App:VOD-Spark  args:
rt: WARN rtRemoteServer.cpp:793 -- Thread-31926: failed to get property: 8907a0a6-ef86-4c3d-aea1-c40c0aa2f6f0. RT_PROP_NOT_FOUND
Select Options:
1) launch Application
2) stop Application
3) query Application status
4) Exit

```
The application will be launched at this point.

Choose 2 to stop the application. You need to provide the same name to stop the app.

Here is a sample that launches youtube
```
root@arrisxi6:~# LighteningCast
LighteningCastService!!!  Func: main
rtLighteningCastRemoteObject(): Service com.comcast.lighteningcast
LIGHTENINGCAST>>>>
Select Options:
1) launch Application
2) stop Application
3) query Application status
4) Exit
rt: WARN rtRemoteServer.cpp:793 -- Thread-31930: failed to get property: 8907a0a6-ef86-4c3d-aea1-c40c0aa2f6f0. RT_PROP_NOT_FOUND
1

Launch Application
Application Name : youtube
Launch Params:
https://www.youtube.com/tv
rtLighteningCastRemoteObject::launchApplication App:youtube  args:https://www.youtube.com/tv
rt: WARN rtRemoteServer.cpp:793 -- Thread-31926: failed to get property: 8907a0a6-ef86-4c3d-aea1-c40c0aa2f6f0. RT_PROP_NOT_FOUND
Select Options:
1) launch Application
2) stop Application
3) query Application status
4) Exit
3

Query Application Status
Application Name : youtube
rtLighteningCastRemoteObject::getApplicationState App:youtube  ID:(null)
rt: WARN rtRemoteServer.cpp:793 -- Thread-31930: failed to get property: 8907a0a6-ef86-4c3d-aea1-c40c0aa2f6f0. RT_PROP_NOT_FOUND
rtLighteningCastRemoteObject::applicationStateChanged
AppName : youtube
State : running
Select Options:
1) launch Application
2) stop Application
3) query Application status
4) Exit

```

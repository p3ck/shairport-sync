Shairport Sync (update-documentation branch)
=============
Shairport Sync emulates an AirPort Express for the purpose of streaming audio from iTunes, iPods, iPhones, iPads and AppleTVs.
Audio played by a Shairport Sync-powered device stays synchronised with the source and hence with similar devices playing the same source. In this way, synchronised multi-room audio is possible without difficulty. (Hence the name Shairport Sync, BTW.)

Shairport Sync does not support AirPlay video or photo streaming.

This branch — "update-documentation" — is a development branch of Shairport Sync and is unstable. To access the stable branch, please switch to the "master" branch.

More Information
----------
Shairport Sync works by using timing information and timestamps present in data coming from the audio source (e.g. an iPhone) to "play" audio at exactly the right time. It does this by monitoring and controlling the "latency" — the time between a sound frame is supposed to be played, as specified by its `timestamp`, and the time when it is actually played by the audio output device, usually a Digital to Audio Converter (DAC).  Timestamps are measured relative to the source computer's clocks, the `source clock`, but timing must be done relative to the clock of the computer running Shairport Sync, the `local clock`. The source and local clocks are synchronised, usually to within a fraction of a millisecond, using a variant of NTP synchronisation protocols.

To maintain the exact latency required, if an output device is running slow relative to the source, Shairport Sync will delete frames of audio to allow the device to keep up. If the output device is running fast, Shairport Sync will insert frames to keep time. The number of frames inserted or deleted is so small as to be almost inaudible on normal audio material. Frames are inserted or deleted as necessary at pseudorandom intervals. Alternatively, with `libsoxr` support, Shairport Sync can resample the audio feed to ensure the output device can keep up. This is less obtrusive than insertion and deletion but requires a good deal of processing power — most embedded devices probably can't support it. The process of insertion/deletion or resampling is rather inelegantly called “stuffing”.

There are four default latency settings, chosen automatically. One latency matches the latency used by recent versions of iTunes when playing audio and another matches the latency used by so-called "AirPlay" devices — iOS devices and by iTunes and Quicktime Player when playing video. A third latency is used when the audio source is `forked-daapd`. The fourth latency is the default if no other latency is chosen and is used for older versions of iTunes.

Shairport Sync is a pretty substantial rewrite of the fantastic work done in Shairport 1.0 by James Laird and others — please see https://github.com/abrasive/shairport/blob/master/README.md#contributors-to-version-1x for a list of the contributors to Shairport 1.x and Shairport 0.x. From a "heritage" point of view, Shairport Sync is a fork of Shairport 1.0.

Shairport Sync is designed for Linux and ALSA. It must have direct access to the output device, which must be a sound card capable of working with 44,100 samples per second interleaved PCM stereo (you'll get a message in the logfile if there's a problem).

For more about the motivation behind Shairport Sync, please see the wiki at https://github.com/mikebrady/shairport-sync/wiki.

What else?
--------------
* Better Volume Control — Shairport Sync offers finer control at very top and very bottom of the volume range. See http://tangentsoft.net/audio/atten.html for a good discussion of audio "attenuators", upon which volume control in Shairport Sync is modelled. See also the diagram of the volume transfer function in the documents folder.
* Hardware Mute — Shairport Sync will mute properly if the hardware supports it.
* Fast Response — With hardware volume control, response is instantaneous; otherwise the response time is 0.15 seconds.
* Non-Interruptible — Shairport Sync sends back a "busy" signal if it's already playing audio from another source, so other sources can't disrupt an existing Shairport Sync session. (If a source disappears without warning, the session automatically terminates after two minutes and the device becomes available again.)
* Metadata — Shairport Sync can be configured to deliver metadata, such as Album Name, Artist Name, Cover Art, etc. through a pipe to a recipient application program — see https://github.com/mikebrady/shairport-sync-metadata-reader for a sample recipient.
* Raw Audio — Shairport Sync can deliver raw PCM audio to standard output or to a pipe. This output is delivered synchronously with the source after the appropriate latency and is not interpolated or "stuffed" on its way through Shairport Sync.
* Autotools and Libtool Support — One important difference between Shairport Sync and other versions of Shairport is that the Shairport Sync build process uses GNU autotools and libtool to examine and configure the build environment — very important for cross compilation. Previous versions of Shairport looked at the current system to determine which packages were available, instead of looking at the target system for what packages were available.

Status
------
Shairport Sync works on a wide variety of Linux devices. It works on standard Ubuntu laptops, on the Raspberry Pi with Raspian, Arch Linux and OpenWrt, and it runs on a Linksys NSLU2 and a TP-Link 710N using OpenWrt. It works with built-in audio and with a variety of USB-connected audio amplifiers and DACs, including a cheapo USB "3D Sound" dongle, a first generation iMic and a Topping TP30 amplifier with a USB DAC input.

Shairport Sync runs well on the Raspberry Pi. It can drive the built-in sound card, though the audio out of the card is of poor quality. USB-connected sound cards work well on later versions of Raspian; however older versions of Raspian appear to suffer from a problem — see http://www.raspberrypi.org/forums/viewtopic.php?t=23544, so it is wise to update. Shairport Sync works well with the IQAudIO Pi-DAC — see http://www.iqaudio.com.

At the time of writing, OpenWrt trunk does not support USB audio well on the Raspberry Pi.

Shairport Sync runs on Ubuntu and Debian inside VMWare Fusion 7 on a Mac, but synchronisation does not work — possibly because the soundcard is being emulated.

Shairport Sync will output to alsa cards, to standard output and to pipes using appropriate backends. You can try compiling additional backends in as you wish, but it definitely will not work properly with them. Maybe someday...

For information about changes and updates, please refer to the RELEASENOTES.md file in the distribution.

Note: Historically, Shairport Sync has taken its settings from command line arguments. While this is still the case, it does not always work well across distributions. Accordingly, from version 2.4 onwards, Shairport Sync reads settings from the file `/etc/shairport-sync.conf`. Access to new settings will be provided only in the settings file.

Building And Installing
---------------------
If you're interested in Shairport Sync for OpenWrt, there's an OpenWrt package at https://github.com/mikebrady/shairport-sync-for-openwrt. OpenWrt doesn't support the IQaudIO Pi-DAC.

An Arch Linux installation package is available (thanks!) at  [EliaCereda/shairport-sync-PKGBUILD](https://github.com/EliaCereda/shairport-sync-PKGBUILD).

Otherwise, follow these instructions.

The following libraries are required:
* OpenSSL or PolarSSL
* Avahi
* ALSA
* libdaemon
* autoconf
* libtool
* libpopt
* libconfig

Optional:
* libsoxr

Many Linux distributions have Avahi and OpenSSL already in place, so normally it probably makes sense to choose those options rather than tinysvcmdns or PolarSSL. Libsoxr is available in recent Linux distributions, but it requires lots of processor power — chances are an embedded processor won't be able to keep up.

Assuming the usual build essentials and git, Debian, Ubuntu and Raspian users can get the basics with:

- `apt-get install autoconf libtool libdaemon-dev libasound2-dev libpopt-dev libconfig-dev`
- `apt-get install avahi-daemon libavahi-client-dev` if you want to use Avahi (recommended).
- `apt-get install libssl-dev` if you want to use OpenSSL and libcrypto, or use PolarSSL otherwise.
- `apt-get install libpolarssl-dev` if you want to use PolarSSL, or use OpenSSL/libcrypto otherwise.
- `apt-get install libsoxr-dev` if you want support for libsoxr-based resampling. This library is not yet part of  Raspian; instructions for how to build it from source are available at [LIBSOXR.md](https://github.com/mikebrady/shairport-sync/blob/2.3/LIBSOXR.md).

Download Shairport Sync:

`git clone https://github.com/mikebrady/shairport-sync.git`

Next, `cd` into the shairport-sync directory and execute the following commands:

```
$ git checkout development #switch to the development branch of shairport-sync
$ autoreconf -i -f
```

Choose the appropriate `--with-*` options:

- `--with-alsa` for the ALSA audio back end. This is required.
- `--with-avahi` or `--with-tinysvcmdns` for mdns support. Avahi is a widely-used system-wide zero-configuration networking (zeroconf) service — it may already be in your system. If you don't have Avahi, or similar, then consider including tinysvcmdns, which is a tiny zeroconf service embedded inside the shairport-sync application itself. To enable multicast for `tinysvcmdns`, you may have to add a default route with the following command: `route add -net 224.0.0.0 netmask 224.0.0.0 eth0` (substitute the correct network port for `eth0`). You should not have more than one zeroconf service on the same system — bad things may happen, according to RFC 6762, §15.
- `--with-ssl=openssl`  or `--with-ssl=polarssl` for encryption and related utilities using either OpenSSL or PolarSSL.
- `--with-soxr` for libsoxr-based resampling.
- `--with-piddir` for specifying where the PID file should be stored. This directory is normally chosen automatically. The directory must be writable. If you use this option, you may have to edit the init script to search for the PID file in your new location.
- `--with-metadata` to add support for Shairport Sync to pipe metadata to a compatible application of your choice. See https://github.com/mikebrady/shairport-sync-metadata-reader for a sample metadata reader.
- `--with-initscript` to install a System V init script at the `make install` stage. Default is to install.
- `--with-configfiles` to install a configuration file and sample at the `make install` stage. Default is to install. An existing `/etc/shairport-sync.conf` will not be overwritten.
- `--with-pkg-config` to use pkg-config to find libraries. Default is to use pkg-config — this option is for special purpose use.

Here is an example, suitable for most installations:

`$ ./configure --with-alsa --with-avahi --with-ssl=openssl --with-metadata --with-soxr`

Omit the `--with-soxr` if the libsoxr library is not available.

`$ make` 

Run `$sudo make install` to install `shairport-sync` along with a default configuration file and a System V startup script to launch it automatically at system startup. The settings are the most basic defaults, so you will want to edit the configuration — the file is `/etc/shairport-sync.conf` — to give the service a name, use a specific sound card, use the hardware mixer and volume control, etc. — there are some examples in the sample configuration file.

Man Page
--------
You can view the man page here: http://htmlpreview.github.io/?https://github.com/mikebrady/shairport-sync/blob/update-documentation/man/shairport-sync.html


Configuring Shairport Sync
--------
There are two logically distinct parts to getting Shairport Sync to run properly on your machine — (1) starting and stopping it and (2) ensuring it has the right settings.

Starting and stopping automatically is taken care of differently in different versions of Linux. By default, when you run `$sudo make install`, a System V startup script is placed at `/etc/init.d/shairport-sync`. This will not be appropriate in Linuxes that use `systemd` such as Arch Linux, so please look at the separate installation scripts for those.

To get the best from Shairport Sync, you’ll need to (1) give Shairport Sync a service name by which it will be seen in iTunes etc., (2) specify the output device to use and (3) specify the name of the mixer volume control to use to control the output level. To get values for (2) and (3) you might need to explore the ALSA output devices with a program like `alsamixer` or similar.

Shairport Sync reads settings from a configuration file at `/etc/shairport-sync.conf`. While it can also take configuration settings from command line options, it is recommended that you use the configuration file method. When you run `$sudo make install`,  a default configuration is installed at `/etc/shairport-sync.conf` (it won't replace an existing one) which should work in almost any system with a sound card.

A sample configuration file is installed (or updated) at `/etc/shairport-sync.conf.sample`. This contains all the setting groups and all the settings available, but they all are commented out (comments begin with `//`) so that default values are used. The file contains explanations of the settings, useful hints and suggestions.

Settings in the configuration file are grouped. For instance, there is a `general` group within which you can use the `name` tag to set the service name. Suppose you wanted to set the name of the service to `Front Room`, give the service the password `secret` and used `libsoxr` interpolation, then you should do the following:

```
general =
{
	name = "Front Room";
	password = "secret";
	interpolation = "soxr";
	// ... other general settings
};
```
The `alsa` group is used to specify properties of the output device. The most obvious setting is the name of the output device which you can set using the `output_device` tag.

The following `alsa` group settings are very important for maximum performance. If your audio device has a hardware mixer and volume control, then Shairport Sync can use them to give instant response to volume and mute commands and it can offload some work from the processor.
* The `mixer_device` tag allows you specify where the mixer is. By default, the mixer is to be found where you specify with the `output_device` tag, so you only need to use the `mixer_device` tag if the mixer is elsewhere. This can happen if you specify a *device* rather than a *card* with the `output_device` tag, because normally a mixer is associated with a *card* rather than a device. For example, if you specified that the output device was device 5 of card hw:0 and if the mixer was associated with the card, you would write `output_device = "hw:0,5";` and `mixer_device = "hw:0";`. 
* The `mixer_type` tag allows you to specify the type of audio mixer -- `software` (default) or `hardware`.
* The `mixer_control_name` tag allows you to specify the name of the volume control on the hardware mixer.

Say you wish to use the output device 5 of card hw:0 and the mixer volume-control named "PCM":

```
alsa =
{
  output_device = "hw:0,5";
  mixer_device = "hw:0";
  mixer_type = "hardware";
  mixer_control_name = "PCM";
  // ... other alsa settings
};
```

You can make Shairport Sync run programs just before it starts to play an audio stream and just after it finishes using the `sessioncontrol` group settings `run_this_before_play_begins` and `run_this_after_play_ends`.
This is to facilitate situations where something has to be done before and after playing, e.g. switching on an amplifier beforehand and switching it off afterwards.

Set the `wait_for_completion` value to `"yes"` for Shairport Sync to wait until the respective commands have been completed before continuing.

Please note that the full path to the programs must be specified, and script files will not be executed unless they are marked as executable and have the standard `#!/bin/...` first line. (This behaviour may be different from other Shairports.)

Command Line Arguments
--------

You can use command line arguments to provide settings to Shairport Sync as before. For full information, please read the Shairport Sync `man` page, also available at  http://htmlpreview.github.io/?https://github.com/mikebrady/shairport-sync/blob/update-documentation/man/shairport-sync.html.

Apart from the following options, all command line options can be replaced by settings in the configuration file. Here is a brief description of command line options that are not replicated by settings in the settings file.

* The `-c` option allows you to specify the location of the configuration file — default is `/etc/shairport-sync.conf`.
* The `-V` option gives you version information about  Shairport Sync and then quits.
* The `-d` option causes Shairport Sync to properly daemonise itself, that is, to run in the background. You may need sudo privileges for this.
* The `-k` option causes Shairport Sync to kill an existing Shairport Sync daemon and then quit. You need to have sudo privileges for this.

The System V init script at `/etc/init.d/shairport-sync` has a bare minimum :
`-d`. Basically all it does is put the program in daemon mode. The program will read its settings from the configuration file, normally `/etc/shairport-sync.conf`.

*Examples*

Here are some examples of complete configuration files. 

```
general = {
  name = "Joe's Stereo";
};

alsa = {
  output_device = "hw:0";
};
```

This gives the service a particular name — "Joe's Stereo" and specifies that audio device hw:0 be used.

For best results — including getting true mute and instant response to volume control and pause commands — you should access the hardware volume controls. Use `amixer` or `alsamixer` or similar to discover the name of the volume controller to be used after the `-c` option.

Here is an example for for a Raspberry Pi using its internal soundcard — device hw:0 — that drives the headphone jack:
```
general = {
  name = "Mike's Boombox";
};

alsa = {
  output_device = "hw:0";
  mixer_type = "hardware";
  mixer_control_name = "PCM";
};
```

Here is an example of using soxr-based resampling and driving a Topping TP30 Digital Amplifier, which has an integrated USB DAC and which is connected as audio device `hw:1`:
```
general = {
  name = "Kitchen";
  interpolation = "soxr";
};

alsa = {
  output_device = "hw:1";
  mixer_type = "hardware";
  mixer_control_name = "PCM";
};
```

For a cheapo "3D Sound" USB card (Stereo output and input only) on a Raspberry Pi:
```
general = {
  name = "Front Room";
};

alsa = {
  output_device = "hw:1";
  mixer_type = "hardware";
  mixer_control_name = "Speaker";
};
```

For a first generation Griffin iMic on a Raspberry Pi:
```
general = {
  name = "Attic";
};

alsa = {
  output_device = "hw:1";
  mixer_type = "hardware";
  mixer_control_name = "PCM";
};
```

For an NSLU2, which has no internal soundcard, there appears to be a bug in ALSA — you can not specify a device other than "default". Thus:

On an NSLU2, to drive a first generation Griffin iMic:
```
general = {
  name = "Den";
};

alsa = {
  mixer_type = "hardware";
  mixer_control_name = "PCM";
};
```

On an NSLU2, to drive the "3D Sound" USB card:
```
general = {
  name = "TV Room";
};

alsa = {
  mixer_type = "hardware";
  mixer_control_name = "Speaker";
};
```

Latency
-------
Latency is the exact time from a sound signal's original timestamp until that signal actually "appears" on the output of the audio output device, usually a Digital to Audio Converter (DAC), irrespective of any internal delays, processing times, etc. in the computer. From listening tests, it seems that there are three latencies in current use:
* If the source is iTunes 10 or later, a latency of 99,400 frames seems to bring Shairport Sync into exact synchronisation both with the speakers on the iTunes computer itself and with AirPort Express receivers.
* If the source is an AirPlay device, the latency seems to be exactly 88,200 frames. AirPlay devices include AppleTV, iPod, iPad and iPhone and Quicktime Player on Mac. 
* If the source is a `forked-daapd`-powered device, the latency seems to be exactly 99,400 frames.
* If the source cannot be identified as AirPlay or as iTunes 10 or later, then the default latency of 88,200 frames seems to work in general. Note that some third party programs masquerade as older versions of iTunes.

Shairport Sync uses the latencies described above as defaults. You shouldn't need to change them.

Problems can arise when you are trying to synchronise with speaker systems — typically surround-sound home theatre systems — that have their own inherent delays. You can compensate for an inherent delay using the `alsa` group `audio_backend_latency_offset`. Set this offset (in frames) to compensate for a fixed delay in the audio back end, for example, if the output device delays by 100 ms, set this to -4410.

Resynchronisation
-------------
Shairport Sync actively maintains synchronisation with the source. 
If synchronisation is lost — say due to a busy source or a congested network — Shairport Sync will mute its output and resynchronise. The loss-of-sync threshold is a very conservative 50 ms — i.e. the actual time and the expected time must differ by more than 50 ms to trigger a resynchronisation. Smaller disparities are corrected by insertions or deletions, as described above.
* You can vary the resync threshold, or turn resync off completely, with the `general` `resync_threshold` setting.

Tolerance
---------
Playback synchronisation is allowed to wander a small amount before attempting to correct it. The default is 88 frames, i.e. 2 ms. The smaller the tolerance, the  more  likely it is that overcorrection  will  occur. Overcorrection is when more corrections (insertions and deletions) are made than are strictly necessary  to  keep the stream in sync. Use the statistics setting to monitor correction levels. Corrections should  not  greatly exceed net corrections.
* You can vary the tolerance with the `general` `drift` setting.

Some Statistics
---------------
If you turn on the `general`  `statistics` setting, statistics like this will be printed at intervals on the console (or in the logfile if running in daemon mode):

`Sync error: -35.4 (frames); net correction: 24.2 (ppm); corrections: 24.2 (ppm); missing packets 0; late packets 5; too late packets 0; resend requests 6; min DAC queue size 4430.`

"Sync error" is the average deviation from exact synchronisation. The example above indicates that the output is on average 35.4 frames ahead of exact synchronisation. Sync is allowed to wander by the tolerance — 88 frames (± 2 milliseconds) by default — before a correction will be made.

"Net correction" is actually the net sum of corrections — the number of frame insertions less the number of frame deletions — given as a moving average in parts per million. After an initial settling period, it represents the divergence between the source clock and the sound device's clock. The example above indicates that the output DAC's clock is running 24.2 ppm faster than the source's clock.

"Corrections" is the number of frame insertions plus the number of frame deletions (i.e. the total number of corrections), given as a moving average in parts per million. The closer this is to the absolute value of the drift, the fewer "unnecessary" corrections that are being made. Third party programs tend to have much larger levels of corrections.

For reference, a drift of one second per day is approximately 11.57 ppm. Left uncorrected, even a drift this small between two audio outputs will be audible after a short time. The above sample is from a second-generation iPod driving the Raspberry Pi which is connected over Ethernet.

It's not unusual to have resend requests, late packets and even missing packets if some part of the connection to the Shairport Sync device is over WiFi. Sometimes late packets can be asked for and received multiple times. Sometimes late packets are sent and arrive too late, but have already been sent and received in time, so weren't needed anyway...

"Min DAC queue size" is the minimum size the queue of samples in the output device's hardware buffer was measured at. It is meant to stand at 0.15 seconds = 6,615 samples, and will go low if the processor is very busy. If it goes below about 2,000 then it's a sign that the processor can't really keep up.

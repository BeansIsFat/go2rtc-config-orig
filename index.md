---
layout: home
title: Video streaming using go2rtc on a Raspberry Pi
---
# Video streaming using go2rtc on a Raspberry Pi

This guide should work on Raspberry Pi Zero 2 W, 3, and 4.  
The Raspberry Pi 5 does not have a hardware video encoder so this configuration will not work as-is.[^pi5]

[^pi5]: At a minimum you'd need to use a different `vcodec` for ffmpeg in `go2rtc.yaml`. I don't a Pi 5 to test it but it looks like you can remove replace `h264_v4l2m2m` with `libx264`. You should probably also remove `#hardware` from the end of the `linux_usbcam` line.

## Install software

Download and install the release labeled `go2rtc_linux_arm64` from [https://github.com/AlexxIT/go2rtc/releases](https://github.com/AlexxIT/go2rtc/releases). You can use these commands for the current release:

{% highlight shell %}
wget https://github.com/AlexxIT/go2rtc/releases/download/v1.9.8/go2rtc_linux_arm64
sudo mv ./go2rtc_linux_arm64 /usr/local/bin/go2rtc
sudo chmod +x /usr/local/bin/go2rtc
{% endhighlight %}

*Important note*: You may also need to update ffmpeg. Check your version by running `ffmpeg -version`. If it's not at least version 5.0 you'll need to update it. Older versions of Raspberry Pi OS have an outdated ffmpeg package so you may have to install it manually.

There are several ways to do this but I prefer the builds and instructions available here: [https://johnvansickle.com/ffmpeg/](https://johnvansickle.com/ffmpeg/)

## Configure go2rtc

Create the following two files. I have this configured for a Raspberry Pi Camera and a USB camera. You'll probably want to delete the `picam_h264` line unless you're using a Raspberry Pi Camera. Replace the path after `video=` with the path to your camera, which you should have from configuring Crowsnest.

File: `/home/pi/printer_data/config/go2rtc.yaml`

{% highlight yaml %}
streams:
  linux_usbcam: ffmpeg:device?video=/dev/v4l/by-id/usb-Ruision_UVC_Camera_20200623-video-index0&input_format=yuyv422&w=1280&h=720#video=h264#hardware
  picam_h264: exec:libcamera-vid --width 1640 --height 1232 --awb daylight --sharpness 6.0 --ev 10 --exposure sport -t 0 --inline -o -
  
ffmpeg:
  h264: "-vcodec h264_v4l2m2m -b:v 6M -an"

api:
  origin: "*"
{% endhighlight %}

File: `/home/pi/printer_data/systemd/go2rtc.env`:  

{% highlight conf %}
GO2RTC_ARGS="-config /home/pi/printer_data/config/go2rtc.yaml"
{% endhighlight %}

## Configure systemd service

Create the following file. You'll need to use `sudo` for this. If your username isn't `pi`, set your actual username after `User=`.

File: `/etc/systemd/system/go2rtc.service`:

{% highlight conf %}
[Unit]
Description=go2rtc for webcam streaming
After=udev.service network-online.target nss-lookup.target
Wants=udev.service network-online.target
StartLimitBurst=10
StartLimitIntervalSec=180

[Install]
WantedBy=multi-user.target

[Service]
Type=simple
User=pi
RemainAfterExit=Yes
WorkingDirectory=/home/pi/go2rtc
EnvironmentFile=/home/pi/printer_data/systemd/go2rtc.env
ExecStart=/usr/local/bin/go2rtc $GO2RTC_ARGS
Restart=on-failure
RestartSec=5
Nice=10
IOSchedulingClass=idle
IOSchedulingPriority=7
CPUWeight=20
AllowedCPUs=1-2
MemoryMax=250M
{% endhighlight %}

Now you can start go2rtc by running

{% highlight shell %}
sudo systemctl start go2rtc
{% endhighlight %}

## Test go2rtc

The go2rtc web interface is at http://YOUR\_IP\_ADDRESS:1984/

Your camera should be listed. Check the stream link in the row for `linux_usbcam`. If something isn't working, you'll have to troubleshoot, starting with the `streams` entry in `go2rtc.yaml`. You can use the `Config` button at the top of the go2rtc web interface to do this.

## Enable systemd service

Once everything is working, you can enable the systemd service so that it starts on every boot.

{% highlight shell %}
sudo systemctl enable go2rtc
{% endhighlight %}

## Configure Mainsail and Fluidd

Make sure you select `WebRTC (go2rtc)` as the service and use the following URL for the stream, replacing the ALL_CAPS text with your IP address or URL:

{% highlight shell %}
http://YOUR_IP_ADDRESS:1984/stream.html?src=linux_usbcam&mode=webrtc
{% endhighlight %}

This is URL for snapshots:

{% highlight shell %}
http://YOUR_IP_ADDRESS:1984/api/frame.jpeg?src=linux_usbcam
{% endhighlight %}

***

### Notes

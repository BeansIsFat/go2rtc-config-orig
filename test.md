Just wanted to test the markdown features of kramdown.

{ highlight text mark_lines="4 9 11" }
/
├── etc
│   └── systemd
│       └── system
│           └── go2rtc.service
└── home
    └── [USER]
        └── printer_data
            ├── systemd
            │   └── go2rtc.env
            └── config
                └── go2rtc.yaml
{% endhighlight %}

> [!NOTE]
> Highlights information that users should take into account, even when skimming.
> And supports multi-line text.

> [!TIP]
> Optional information to help a user be more successful.

> [!IMPORTANT]  
> Crucial information necessary for users to succeed.

  > [!WARNING]  
  > Critical content demanding immediate
  > user attention due to potential risks.

> [!CAUTION]
> Negative potential consequences of an action.
> Opportunity to provide more context.


Here's a code block

```
sudo rm -rf
```

And one with syntax highlighting

~~~ yaml
streams:
  linux_usbcam: ffmpeg:device?video=/dev/v4l/by-id/usb-Ruision_UVC_Camera_20200623-video-index0&input_format=yuyv422&w=1280&h=720#video=h264#hardware
  picam_h264: exec:libcamera-vid --width 1640 --height 1232 --awb daylight --sharpness 6.0 --ev 10 --exposure sport -t 0 --inline -o -
  
ffmpeg:
  h264: "-vcodec h264_v4l2m2m -b:v 6M -an"

api:
  origin: "*"
~~~

And the same block with Rogue syntax highlighting

{% highlight yaml %}
streams:
  linux_usbcam: ffmpeg:device?video=/dev/v4l/by-id/usb-Ruision_UVC_Camera_20200623-video-index0&input_format=yuyv422&w=1280&h=720#video=h264#hardware
  picam_h264: exec:libcamera-vid --width 1640 --height 1232 --awb daylight --sharpness 6.0 --ev 10 --exposure sport -t 0 --inline -o -
  
ffmpeg:
  h264: "-vcodec h264_v4l2m2m -b:v 6M -an"

api:
  origin: "*"
{% endhighlight %}

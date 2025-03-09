Just wanted to test the markdown features of kramdown.

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

# ESP32-EPDSign

This is a general purpose e-paper sign module. It also allows for modules with WS2812 RGB LEDs (e.g. [7.5" Waveshare driver](https://www.amazon.co.uk/dp/B0CPYJ19NM)).

It can be compiled to work with a variety of panels as per [GFX library](https://github.com/revk/ESP32-GFX).

It uses the [RevK library](https://github.com/revk/ESP32-RevK) so handles MQTT commands and settings as per that library.

It checks `imageurl` every minute, and if it has changed it updates the display.

It can also overlay a date/time on the bottom of the display if `showtime` is set.

## Image files

The image file is expected to be a binary file for the frame buffer for the display. If the display is set to be inverted (i.e. `gfxinvert` is set) then this is inverted after loading. However the orientation has to be as per the display, i.e. `gfxflip` is not applied to this image. Note `gfxflip` is applied for any text overlay and the clock overlay. It shows text when powered on to confirm IP address, etc.

### Creating an image file

The simplest way to make an image file is using ImageMagick, for example.

```
convert image.png -dither None -monochrome -rotate -90 -depth 1 GRAY:image.mono
```

That example rotates the image, which is obviously only necessary of the display orientation is not what you expect. You may also want to scale, centre, and crop the image to the required size as needed as part of that command.

The use of `--depth 1` and `GRAY:` may seem odd, why not `--mono`, but the reason is that the bit order is different. Doing it this way matches the display (bit 7 left, bit 0 right).

ImageMagick can convert a wide variety of file formats.

### White/Black/Red

For displays that are there colour you need to make the black and red images and concatinate (red after black).

### Atomic update

It is recommended that you make the new file in the same file system, e.g. `image.new` and use `mv` to replace the existing image. This ensures the update is atomic at a file system level and the display will not see a partly written image when served by apache.

### http

The module can use `https` (with letsencrypt certificate), but it is recommended you use `http` on a local network if you can do so safely, as `https` uses a lot more resources on the ESP module.
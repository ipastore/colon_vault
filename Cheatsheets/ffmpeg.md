# Command History
Quiero hacer hacer 2 crops en un video: 

1. Do a crop of specific pixels with a different ceter: crop 70 pixels from left, 20 pixels from right, 34 pixels from top and 34 pixels from bottom.
2. Then do a center crop with the final output being 1344 of width and 992 height

The original images is 1440W x 1080W

```bash
ffmpeg -i Seq_035_b.mp4 -vf "crop=1350:1012:70:34,crop=1344:992:0:0" -c:v libx264 -crf 18 output_cropped.mp4
```

el crop lo hace con w:h:x:y

w= el width final que se quiere
h= el height final que se quiere
x,y = el centro desde donde se hace el crop

Elegir desde perframe dir
Luego modificar la vignetta
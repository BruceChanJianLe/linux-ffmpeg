# Linux ffmpeg

This repository stores some tricks for using ffmpeg.

## Converting

To windows format,
```sh
ffmpeg -i in.mp4 -pix_fmt yuv420p -c:a copy -movflags +faststart out.mp4
ffmpeg -y -i input_file.mp4 -c:v libx264 -c:a aac -strict experimental -tune fastdecode -pix_fmt yuv420p -b:a 192k -ar 48000 output_file.mp4
ffmpeg -i input.mp4 -c:v libx265 -crf 26 -preset fast -c:a aac -b:a 128k output.mp4
# When error occurs, use the command below
# [libx264 @ 0x55add09ab9e0] height not divisible by 2 (2112x1207)
# Error initializing output stream 0:0 -- Error while opening encoder for output stream #0:0 - maybe incorrect parameters such as bit_rate, rate, width or height
ffmpeg -y -i input.mp4 -vf "pad=ceil(iw/2)*2:ceil(ih/2)*2" -c:v libx264 -c:a aac -strict experimental -tune fastdecode -pix_fmt yuv420p -c:a 192k -ar 48000 out.mp4
```

Reference
- (https://trac.ffmpeg.org/wiki/Encode/H.265)
- (https://stackoverflow.com/questions/20847674/ffmpeg-libx264-height-not-divisible-by-2)

## Cropping

```sh
ffmpeg -i in.mp4 -filter:v "crop=out_w:out_h:x:y" out.mp4
```
  Where:
	“in.mp4” refers to the input file to be converted
	“out.mp4” is the name of the output file to be saved after conversion
	out_w is the width of your desired output rectangle to which the original video’s width will be reduced
	out_h is the height of your output rectangle to which the original video’s height will be reduced
	x and y are the position coordinates for the top left corner of your desired output rectangle
	If you want to crop a 1280×720 rectangle from a 1920×1080 resolution video with a starting rectangle position of 10, 10; your command would be:
	Ex: ffmpeg -i in.mp4 -filter:v "crop=1280:720:10:10" out.mp4
  
## Cutting

```sh
ffmpeg -ss 00:01:00 -i input.mp4 -to 00:02:00 -c copy output.mp4
```
  Where:
	-i: This specifies the input file. In that case, it is (input.mp4).
	-ss: Used with -i, this seeks in the input file (input.mp4) to position.
	00:01:00: This is the time your trimmed video will start with.
	-to: This specifies duration from start (00:01:40) to end (00:02:12).
	00:02:00: This is the time your trimmed video will end with.
	-c copy: This is an option to trim via stream copy. (NB: Very fast)
	The timing format is: hh:mm:ss

## Resizing Images

```sh
ffmpeg -i input.png -vf scale=17:17 output.png
ffmpeg -i input.png -vf scale="iw/1:ih/2" output.png

```
  Where:
	-i: This specifies the input file. In that case, it is (input.mp4).
	-vf: is filter_graph which set video filters, here we use scale as the filter.
	scale: This is the scale you want for the image (width:height).
	iw: input width
	ih: input height
	
## Reducing mp4 File Size

```sh
ffmpeg -i input.mp4 -vcodec libx265 -crf 40 output.mp4
```

The lower the crf number the higher the resolution.

[reference](https://unix.stackexchange.com/questions/28803/how-can-i-reduce-a-videos-size-with-ffmpeg)

## Speed up Video

```sh
# To double the speed of the video with the setpts filter, you can use the below command, but note that it will drop frames
ffmpeg -i input.mkv -filter:v "setpts=0.5*PTS" output.mkv

# You can avoid dropped frames by specifying a higher output frame rate than the input. For example, to go from an input of 4 FPS to one that is sped up to 4x that (16 FPS):
ffmpeg -i input.mkv -r 16 -filter:v "setpts=0.25*PTS" output.mkv

# To slow down your video, you have to use a multiplier greater than 1:
ffmpeg -i input.mkv -filter:v "setpts=2.0*PTS" output.mkv
```

[reference](https://trac.ffmpeg.org/wiki/How%20to%20speed%20up%20/%20slow%20down%20a%20video)

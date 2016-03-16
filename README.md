# Video and Image Manipulation on the Command Line

## ffmpeg

### Getting Help

`ffmpeg -h encoder=tiff`

`ffmpeg -h encoder=mjpeg`

`ffmpeg -pix_fmts > %filepath%\formats.txt see all the formats`

`ffprobe -i input.mp4`  
ffprobe get's video metadata

####4K H.265 

`ffmpeg -y -i file.%%04d.tif -c:v libx265 -x265-params bitrate=25000:vbv-bufsize=8000:vbv-maxrate=40000 -pix_fmt yuv420p -movflags +faststart -f mp4 name.mp4`


####4k H.265 (less compression) 

`ffmpeg -y -i in.mov -c:v libx265 -x265-params bitrate=27000:vbv-bufsize=8000:vbv-maxrate=40000 -pix_fmt yuv420p -movflags +faststart -f mp4 name.mp4`


####H.264 mp4 

`ffmpeg -y -i in.mov -c:v libx264 -x264-params bitrate=25000:vbv-bufsize=8000:vbv-maxrate=30000 -pix_fmt yuv420p -movflags +faststart -f mp4 name.mp4`


####APPLE PRORES (Best for files playing on a Mac) 

`ffmpeg -y -i in.mov -vcodec prores -profile:v 3 -r "30" -c:a mp2 %filepath%\MKprores.mov`


####PHOTO JPG (Best for files playing on a PC) 

`ffmpeg -y -r 30 -start_number 1 -i %file_path%/%project%.%04d.tif -pix_fmt yuvj420p -vframes 10 -vcodec mjpeg -q:v 7 -f mov name.mov`

Other options:
12 bit yuvj420p  Adobe uses yuvj420p, files smaller
16 bit yuvj422p
24 bit yuvj444p double size


####TIFF to TIFF compress LZW and change to grayscale 

`ffmpeg -y -i file.%%04d.tif -compression_algo lzw -pix_fmt gray out.%%04d.tif`

####Scale 

`ffmpeg -i input.mp4 -vf scale=newW:newH out.mp4`

####Nearest Neighbor Scaling

`ffmpeg -i in.mov -sws_flags neighbor -vf scale=newWidth:newHeight out.mov`

####Pipe IO

How to send and receive data to/from named pipes (mac / linux only)

Make two pipes

`mkfifo /tmp/pipe1 /tmp/pipe2`

Send input1 to pipe1

`ffmpeg -y -i input1.mp4 -c copy -f nut /tmp/pipe1`

In another shell window

`ffmpeg -y -i input2.mp4 -c copy -f nut /tmp/pipe2`

In another shell concat the videos

`ffmpeg -y -i /tmp/pipe1 -i /tmp/pipe2 -filter_complex concat=n=2:v=1 out.mov`

Might be possible to do this in one shell by appending a single ampersand to the end of each ffmpeg command.

####Add title bars

`ffmpeg -i in.mp4 -vf pad=newWidth:newHeight:newX:newY out.mp4`

Default pad color is black

newX and newY are how far to move (0,0) from it's old origin.

See: https://ffmpeg.org/ffmpeg-filters.html#Examples-65

####Make extra long looping video
`for i in {1..60}; do printf "file '%s'\n" videoToLoop.mp4 >> list.txt; done`

`ffmpeg -f concat -i list.txt -c copy out.mp4`

See: https://trac.ffmpeg.org/wiki/Concatenate

####Mux audio and video (without re-encoding)

`ffmpeg -i video.mp4 -i music.mp3 -c copy -map 0:v:0 -map -map 1:a:0 -shortest out.mp4`

Muxing an mp4 with mp3 won't work in all players, but audio can be converted to wav or aac if you need more compatibility.

####Crop Video

`ffmpeg -i input.mp4 -filter:v "crop=out_w:out_h:x:y out.mp4`

X and Y denote top left of output. 

####Trim Video

`ffmpeg -i input.mp4 -ss 00:00:03 -t 00:01:03 -codec copy -async 1 out.mp4`

SS is the new start time of the video

T is the new length of the video

Codec copy is faster than decoding/recoding

async 1 is needed to ensure audio is correctly chopped

See: http://stackoverflow.com/questions/18444194/cutting-the-videos-based-on-start-and-end-time-using-ffmpeg  
See: https://trac.ffmpeg.org/wiki/Seeking

####Invert Colors

`ffmpeg -i input.mp4 -vf lutrgb="r=negval:g=negval:b=negval" inverted.mp4`

####Rotate

`ffmpeg -i input.mp4 -vf rotate=90 rotated90Degrees.mp4`

Rotate is in degrees

## imagemagick

####Getting help

`convert -h`

`identify -list format`
Will show you supported formats on your machine

####Show image information

`identify -verbose image.jpg`

####Convert file
`convert in.jpg out.tif`

Can be any image file type in or out (unless RAW)

####Batch Resize

`mogrify -path outputFolder -resize newWidthxnewHeight *.png`

Output folder is an already existing destination

####Batch LZW Compression

`mogrify -path outFolder -depth 16 -endian msb -compress lzw *.tif`

## ufraw
http://ufraw.sourceforge.net/

Ufraw is useful if you don't have adobe camera raw or need to convert raw to other file formats. Imagemagick relies on it for some conversion.

`brew install ufraw`

####Batch Conversion

```#!/bin/bash
i=0
mkdir converted
for file in ~/Desktop/pics/*.ARW
do
	#echo "resized/$i.jpg"
	ufraw-batch --output="converted/$i.jpg" "$file"
	i=$((i+1))
done
```


## Other References
https://en.wikibooks.org/wiki/FFMPEG_An_Intermediate_Guide/image_sequence

http://www.stuudio.ee/anothergui/presets.html

https://ffmpeg.org/ffmpeg.html#Main-options

http://randombio.com/linuxsetup141.html

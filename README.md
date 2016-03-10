# Video and Image Manipulation on the Command Line

## ffmpeg

### Getting Help

`ffmpeg -h encoder=tiff`

`ffmpeg -h encoder=mjpeg`

`ffmpeg -pix_fmts > %filepath%\formats.txt see all the formats`



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
`ffmpeg -y -i /tmp/pipe1 -i /tmp/pipe2 -filter_complex concat=n=2:v=1 out.mov

Might be possible to do this in one shell by appending a single ampersand to the end of each ffmpeg command.


## imagemagick

####Convert file
`convert in.jpg out.tif`

Can be any image file type in or out (unless RAW)

####Batch Resize

`mogrify -path outputFolder -resize newWidthxnewHeight *.png`

Output folder is an already existing destination


### Other References
https://en.wikibooks.org/wiki/FFMPEG_An_Intermediate_Guide/image_sequence
http://www.stuudio.ee/anothergui/presets.html

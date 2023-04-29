# v380
Extract h264 from v380 camera where you can use it to feed another application

# Help
```
OPTIONS:
  -u              username         (default admin)
  -p              password
  -addr           camera IP/address
  -mac            camera MAC address
  -id             camera ID
  -port           camera port      (default 8800)
  -retry          Number of connection attempt (default 5)
  --enable-ptz=0  Disable pan-tilt-zoom via keyboard press
  --discover      Discover camera
  -h              Show this help
```

# Usage example
The camera can be connected either via -id, -addr or -mac. When specifying both -id and -addr, the software will skip discovering and connect straight to camera. Please specify both -id and -addr if the app have trouble discovering your camera.

### Connect by IP and feed it to ffplay
`./v380 -u admin -p password -port 8800 -addr 192.168.1.2 | ffplay -vcodec h264 -probesize 32 -formatprobesize 0 -avioflags direct -flags low_delay -i -`
### Connect by id and feed it to ffplay
`./v380 -u admin -p password -port 8800 -id 123456 | ffplay -vcodec h264 -probesize 32 -formatprobesize 0 -avioflags direct -flags low_delay -i -`
### Connect by mac address and feed it to ffmpeg to ffserver
`./v380 -p password -mac 11:22:33:aa:bb:cc | ffmpeg -i - http://localhost:8090/feed1.ffm`
### Feed to gstreamer hosting mjpeg
The http server can be found here: https://gist.github.com/misaelnieto/2409785  
`./v380 -p password -mac 11:22:33:aa:bb:cc | gst-launch-1.0 -v fdsrc ! decodebin ! jpegenc ! multipartmux boundary=spionisto ! tcpclientsink host=127.0.0.1 port=9999
`

### Save to video file with certain duration and valid name
`timeout 1m ./v380 -u admin -p password -port 8800 -id 123456 -addr 192.168.1.1 > VID_$(date +'%Y%m%d_%H%M%S').avi`
### The camera can be discovered by using `--discover` command
`v380 --discover`

# Compiling
Use Visual Studio 2015 or on linux use `make` under v380 subdirectory

Cross compiling for armv5:
(Dynamically linked v380 binary for armv5tej architecture)

Building: Install required package arm-linux-gnueabi-g++ compiler with `apt install g++-arm-linux-gnueabi` command, Then replace `Makefile` in v380 subdirectory with this one in realeses and run `make` command to building dynamically linked v380 binary for armv5tej architecture. If you want to building statically linked v380 binary edit `Makefile` and add `-static` switch to Line 2 after `-lpthread` switch

Executing: Copy shard libraries to `/lib` or binary v380 working directory, In case if you copy shared libraries to binary v380 working directory then run `./ld-linux.so.3 --library-path . ./v380` to executing 

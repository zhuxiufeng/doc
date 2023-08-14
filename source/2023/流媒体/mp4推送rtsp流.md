# gst-rtsp-server 推送mp4文件
## 准备环境
- Ubuntu 18.04
- gst-rtsp-server: <https://gstreamer.freedesktop.org/src/gst-rtsp-server/>

## 安装依赖
```bash
sudo apt-get install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev
```
## 编译gst-rtsp-server
```bash
wget https://gstreamer.freedesktop.org/src/gst-rtsp-server/gst-rtsp-server-1.14.4.tar.xz
tar -xvf gst-rtsp-server-1.14.4.tar.xz
cd gst-rtsp-server-1.14.4
./configure
make
```
## 运行gst-rtsp-server 推送mp4文件
```bash
cd examples
./test-launch "filesrc location=./X.mp4 ! qtdemux ! queue ! rtph264pay name=pay0 pt=96 config-interval=1"
```
流地址：rtsp://ip:8554/test

## 注意事项
- 如果mp4文件,windows上右击属性,看不到分辨率,那么就需要用HandBrake: <https://handbrake.fr/>转换一下,转换成新的mp4文件,然后再推送

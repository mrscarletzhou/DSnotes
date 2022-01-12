# Code随记
## Docker篇
### 1 - install & uninstall 
+ 查看docker
```
dpkg -l | grep -i docker
```
+ 删除docker
```
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce  
```
+ 查看可安装版本
```
apt-cache madison docker-ce
```
+ 选择想安装的版本
```
sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io
```
+ 测试是否安装成功
```
sudo docker run hello-world
docker version
```

### 2 - errors
1. <font color=red>Error response from daemon: could not select device driver ““ with capabilities: [[gpu]]</font>

    原因未知
    + solution1: reinstall docker
    + solution2: 
    ```
    systemctl restart docker
    ```
## meson篇
### 1 - environment
```
apt-get install python3 python3-pip python3-setuptools python3-wheel ninja-build

pip3 install meson
```
在meson.build文件目录
```
meson build
cd build
ninja
ninja install
```


gst-launch-1.0 v4l2src device=/dev/video0 io-mode=2 ! ‘image/jpeg,width=1920,height=1080,framerate=30/1’ ! jpegparse ! jpegdec ! videoconvert ! nvvideoconvert ! mux.sink_0 nvstreammux live-source=1 name=mux batch-size=1 width=1920 height=1080 ! nvinfer config-file-path=/opt/nvidia/deepstream/deepstream-6.0/sources/pythonapps/apps/deepstream-test1-usbcam/dstest1_pgie_config.txt batch-size=1 ! nvmultistreamtiler rows=1 columns=1 width=1920 height=1080 ! nvvideoconvert ! nvegltransform ! nveglglessink


gst-launch-1.0 v4l2src device=/dev/video0 io-mode=2 ! ‘image/jpeg,width=1920,height=1080,framerate=30/1’ ! jpegparse ! jpegdec ! videoconvert ! nvvideoconvert ! ‘video/x-raw,format=NV12’ ! mux.sink_0 nvstreammux live-source=1 name=mux batch-size=1 width=1920 height=1080 ! nvinfer config-file-path=/opt/nvidia/deepstream/deepstream-6.0/sources/pythonapps/apps/deepstream-test1-usbcam/dstest1_pgie_config.txt batch-size=1 ! nvmultistreamtiler rows=1 columns=1 width=1920 height=1080 ! nvvideoconvert ! nvegltransform ! nveglglessink


gst-launch-1.0 v4l2src device=/dev/video0 io-mode=2 ! image/jpeg,framerate=25/1,width=1920,height=1080 ! jpegparse ! nvv4l2decoder mjpeg=1 ! nvoverlaysink
# Deepstream笔记
## 1 - links
### 官方文档
+ [deepstream docker dev 6.1 doc](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/deepstream)
+ [Python Bindings and Application Development](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_Python_Sample_Apps.html)
+ [dGPU Setup for Ubuntu](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_Quickstart.html#dgpu-setup-for-ubuntu)
### GitHub
+ [DeepStream Python Apps](https://github.com/NVIDIA-AI-IOT/deepstream_python_apps)
+ [gst-python](https://github.com/GStreamer/gst-python)
+ [pybind11](https://github.com/pybind/pybind11)

## 2 - 环境

+ Ubuntu 20.04

+ GStreamer 1.16.2

+ NVIDIA driver 510.47.03

+ CUDA 11.6

+ TensorRT 8.2.5.1

+ python 3.8

<font color=red>不要使用高版本以防出现未知错误</font>

<font color=red>不要使用docker 20!!!</font>

<font color=red>不要同时使用docker与nvidia docker2！！！</font>

原因查看deepsteam docker官方文档[Prerequisites](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/deepstream)

## 3 - 步骤
1. pull 官方镜像 [A Docker Container for dGPU](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_docker_containers.html)
2. gitclone 官方python-app[DeepStream Python Apps](https://github.com/NVIDIA-AI-IOT/deepstream_python_apps)到宿主机{LOCAL_CODE_PATH}
3. 起容器命令
```
docker run --gpus all -it -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$DISPLAY --net=host -w /opt/nvidia/deepstream/deepstream-6.1/sources/pythonapps -v {LOCAL_CODE_PATH}:/opt/nvidia/deepstream/deepstream-6.1/sources/pythonapps -v {LOCAL_MEDIA_PATH}:/media/ -v {LOCAL_MODEL_PATH}:/opt/models/ --name kbrain_00 nvcr.io/nvidia/deepstream:6.1-devel
```
4. 安装依赖
```
apt install python3-gi python3-dev python3-gst-1.0 python-gi-dev git python-dev \
    python3 python3-pip python3.8-dev cmake g++ build-essential libglib2.0-dev \
    libglib2.0-dev-bin python-gi-dev libtool m4 autoconf automake

export GST_LIBS="-lgstreamer-1.0 -lgobject-2.0 -lglib-2.0"

export GST_CFLAGS="-pthread -I/usr/include/gstreamer-1.0 -I/usr/include/glib-2.0 -I/usr/lib/x86_64-linux-gnu/glib-2.0/include"
```
5. 更新submodules
```
git submodule update --init
```
容器内网络可能会很差，可以在宿主机进行操作

6. 编译gst-python
```
apt-get install -y apt-transport-https ca-certificates -y
update-ca-certificates
cd gst-python
```
切换gst-python版本
```
git checkout 5343aeb
```
编译
```
./autogen.sh PYTHON=python3
./configure PYTHON=python3
make
make install
```
### 常见错误
1. error:No package 'pygobject-3.0' found

不知为何没有正确安装 python-gi-dev，重新apt-get install一次即可

2. 注意!此处只支持python 3.6.x，尝试python 3.8会报错

configure: error: Python libs not found. Windows requires Python modules to be explicitly linked to libpython.

**解决方法[here](https://gitlab.freedesktop.org/gstreamer/gst-python/-/merge_requests/14/diffs?commit_id=1d8e3256116f4e27815cc1778c59550d40419dbb)**

尝试更改为
```
./autogen.sh PYTHON=/usr/local/bin/pthon3
```
依然报错too old

原因是python3.8分离了python-embed文件，导致autogen无法找到该模块。

官方给出的解决方法是

___autotool is dead, long live the meson___

此处尝试使用meson build出现了一堆问题，如：

gst1.0版本太低——因为按照官方tutorial我们安装版本小于最新gst-python git中[meson build](https://github.com/GStreamer/gst-python/blob/master/meson.build)要求的1.19.2

尝试下载gst-build来编译最新的gst-core，失败……

日后再做尝试
git config --global --add safe.directory /opt/nvidia/deepstream/deepstream-6.1/sources/pythonapps/3rdparty/gst-python/common

7. 编译bindings
```
cd deepstream_python_apps/bindings
mkdir build
cd build
cmake ..
make
```
<font color=red>若此处出现提示： 'pyds' not found

则检查上方gst-python步骤与pybind11是否成功clone
</font>

完成后build文件夹中会出现*pyds-1.1.0-py3-none\*.whl*文件，安装它就大功告成啦~
```
pip3 install ./pyds-1.1.0-py3-none*.whl
```



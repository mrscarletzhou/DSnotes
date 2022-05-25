# Deepstream笔记
## 1 - links
### 官方文档
+ [deepstream docker dev 6.0 doc](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/deepstream)
+ [Python Bindings and Application Development](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_Python_Sample_Apps.html)
+ [dGPU Setup for Ubuntu](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_Quickstart.html#dgpu-setup-for-ubuntu)
### GitHub
+ [DeepStream Python Apps](https://github.com/NVIDIA-AI-IOT/deepstream_python_apps)
+ [gst-python](https://github.com/GStreamer/gst-python)
+ [pybind11](https://github.com/pybind/pybind11)

## 2 - 环境

+ Ubuntu 18.04

+ GStreamer 1.14.5

+ NVIDIA driver 470.63.01

+ CUDA 11.4

+ TensorRT 8.0.1

<font color=red>不要使用高版本以防出现未知错误</font>

<font color=red>不要使用docker 20!!!</font>

<font color=red>不要同时使用docker与nvidia docker2！！！</font>

原因查看deepsteam docker官方文档[Prerequisites](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/deepstream)

## 3 - 步骤
1. pull 官方镜像 [A Docker Container for dGPU](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_docker_containers.html)
2. gitclone 官方python-app[DeepStream Python Apps](https://github.com/NVIDIA-AI-IOT/deepstream_python_apps)
3. 
4. 安装依赖
```
apt install -y git python-dev python3 python3-pip python3.6-dev python3.8-dev cmake g++ build-essential libglib2.0-dev libglib2.0-dev-bin python-gi-dev libtool m4 autoconf automake

export GST_LIBS="-lgstreamer-1.0 -lgobject-2.0 -lglib-2.0"

export GST_CFLAGS="-pthread -I/usr/include/gstreamer-1.0 -I/usr/include/glib-2.0 -I/usr/lib/x86_64-linux-gnu/glib-2.0/include"
```
5. 更新submodules
```
git submodule update --init
```
6. 编译gst-python
```
cd gst-python
```
切换gst-python版本
```
git checkout 1a8f48a
```
编译
```
./autogen.sh PYTHON=python3
./configure PYTHON=python3
make
make install
```
<font color=red>
error:No package 'pygobject-3.0' found

不知为何没有正确安装 python-gi-dev，重新apt-get install一次即可

注意!此处只支持python 3.6.x，尝试python 3.8会报错

configure: error: Python libs not found. Windows requires Python modules to be explicitly linked to libpython.

尝试更改为
</font>
```
./autogen.sh PYTHON=/usr/local/bin/pthon3
```
<font color=red>依然报错too old</font>

原因是python3.8分离了python-embed文件，导致autogen无法找到该模块。

官方给出的解决方法是

___autotool is dead, long live the meson___

此处尝试使用meson build出现了一堆问题，如：

<font color=red>gst1.0版本太低——因为按照官方tutorial我们安装版本小于最新gst-python git中[meson build](https://github.com/GStreamer/gst-python/blob/master/meson.build)要求的1.19.2</font>

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

## 4 - 运行容器

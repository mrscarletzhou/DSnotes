# Yolov5 with deepstream 
## 1 - 参考links
+ [官方文档](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_using_custom_model.html)
+ [yolov3 & yolov4 in deepstream 6.0](https://segmentfault.com/a/1190000041021529)
+ [yolov5 in deepstream 5.0](https://blog.csdn.net/zong596568821xp/article/details/109444343)
+ [pgie config properties](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_plugin_gst-nvinfer.html#gst-properties)

## 2 - nvdsinfer_custom_impl_Yolo 结构分析
参考[here](https://zhuanlan.zhihu.com/p/361152600)
+ nvdsinfer_yolo_engine.cpp: 解析模型、生成引擎
+ nvdsparsebbox_Yolo.cpp : 输出层的解析函数，解析目标检测框
+ trt_utils.cpp 和 trt_utils.h : 构造tensorRT网络的工具类的接口和实现
+ yolo.cpp 和 yolo.h : 生成yolo 引擎的接口和实现
+ yoloPlugins.cpp 和 yoloPlugins.h : YoloLayerV3 and YoloLayerV3PluginCreator 的接口和实现
+ kernels cu: cuda核底层实现


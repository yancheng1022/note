# 1、tflite

TensorFlow Lite 是TensorFlow在移动和嵌入式设备上的轻量级推理框架，专门针对资源受限的环境进行了优化，以便在手机、嵌入式设备上运行深度学习模型。
优势：缩减了模型的大小和功耗，适合嵌入式等资源受限的设备。
坑点：tfl 目前主要支持 cnn 相关的算子 ，对 rnn 等其他网络中的算子还没有很好的支持。对自家的tf框架支持比较好，对于其他框架支持不佳


pytorch - onnx - tensorflow -tflite


![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250327220037.png)

浮点版转换：文件大，准确度高
整数量化版转换：文件小，速度快，准确率低

yolo5

python export.py --weights E:\temp\best.pt --include tflite --img 640

python export.py --weights E:\temp\3\best.pt --include tflite --int8 --img 640

yolo8 

代码exportTflite.py


# 2、ncnn

NCNN是腾讯优图实验室开源的高性能神经网络推理框架，专为移动端及嵌入式设备设计，以轻量化、高效率为核心优势


yolo8 

代码exportTflite.py

# 3、kmodel

E:\code\kmodel

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250605235120.png)

python to_kmodel.py --target k230 --model C:\Users\28979\Downloads\code\1\best.onnx --dataset C:\Users\28979\Downloads\code\val --input_width 640 --input_height 640 --ptq_option 0

python to_kmodel.py --target k230 --model E:\temp\5\best.onnx  --dataset C:\Users\28979\Downloads\code\val  --input_width 640 --input_height 640 --ptq_option 0
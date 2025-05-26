扩容优化 - 素数
处理哈希冲突的方式：开放地址法 - 线性探测法  平方探测   二次哈希





参考：https://blog.csdn.net/2401_85556416/article/details/141431698

conda create -n yolov10 python=3.11.4

https://github.com/THU-MIG/yolov10 


https://pytorch.org/get-started/previous-versions/


pip install -r requirements.txt


docker run -d \
  --restart always \
  --name module \
  --hostname module \
  --network default \
  -p 40154:8082 \
  -v /home/lantrack/4015/analyzer/upload:/upload \
  -v /home/lantrack/cdeslogs/analyzer:/cdeslogs \
  39.105.159.2:10443/library/4015-aiva-server-prod/analyzer:0.0.26
  
  docker login https://ai.iqianyao.com:10443 -u admin -p Shanzesun@2023wujiu

docker run -d \
  --restart always \
  --name module \
  --hostname module \
  --network default \
  -p 40153:8082 \
  -v /home/lantrack/4015/module/upload:/upload \
  -v /home/lantrack/cdeslogs/module:/cdeslogs \
  39.105.159.2/library/4015-aiva-server-prod/module:1.0

docker run -d \
  --restart always \
  --name module \
  --hostname module \
  --network default \
  -p 40153:8082 \
  -v /home/lantrack/4015/module/upload:/upload \
  -v /home/lantrack/cdeslogs/module:/cdeslogs \
  39.105.159.2:10443/library/4015-aiva-server-prod/module:1.0

ragflow
pdf的解析会有内存泄漏现象，deepdoc    用Plain Text会快一些

幻觉问题
检索结果不相关或信息不足时模型更容易幻觉
当最相关文档相似度低于阈值时拒绝回答

1、dify后台问题
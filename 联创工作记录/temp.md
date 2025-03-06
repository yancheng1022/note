参考：https://blog.csdn.net/2401_85556416/article/details/141431698

conda create -n yolov10 python=3.11.4

https://github.com/THU-MIG/yolov10 


https://pytorch.org/get-started/previous-versions/


pip install -r requirements.txt


docker run -d \
  --restart always \
  --name analyzer \
  --hostname analyzer \
  --network default \
  -p 40154:8083 \
  -v /home/lantrack/4015/analyzer/upload:/upload \
  -v /home/lantrack/cdeslogs/analyzer:/cdeslogs \
  39.105.159.2:10443/library/4015-aiva-server-prod/analyzer:0.0.25
  
  docker login https://ai.iqianyao.com:10443 -u admin -p Shanzesun@2023wujiu

- AWS Config提供AWMS的资源清单，以及这些资源的变动记录。
- 通过AWS Config定义规则，来评估针对某个资源变动的合规性compliance

![](http://tvax3.sinaimg.cn/large/006gDTsUgy1g9n2o76fauj311x0f5wgs.jpg)

![](http://tva2.sinaimg.cn/large/006gDTsUgy1g9n2oxhbx1j311v0dwjsn.jpg)


- waitong for 5-10mins

![image](https://user-images.githubusercontent.com/26485327/70409787-5e6c7380-1a90-11ea-9e6e-3a9606cb598f.png)

- Only scan AWS resource in the same Region with Config
  - One S3 in N.Virginia
  - Config get only 3 buckets without this one
  
![image](https://user-images.githubusercontent.com/26485327/70409977-e3f02380-1a90-11ea-859f-d1a67e47eeaf.png)
![image](https://user-images.githubusercontent.com/26485327/70410005-f4080300-1a90-11ea-8b72-d5ba806c8621.png)


- 关闭Config功能

![image](https://user-images.githubusercontent.com/26485327/70410075-2ade1900-1a91-11ea-8d7e-b92562e56510.png)

# CloudWatch 101

- **CloudTrail monitors API calls in the AWS platform**，因为任何一个账号创建EC2或是S3等资源时，都会想AWS发送一个API call
- auditing v.	审计; 稽核

1. CLoudWatch menu
2. 创建警报
3. 选择指标
4. EC2 - 每个实例的指标
5. 搜索需要监控的EC2实例id - 选择CPUUtilization
6. 输入警报指标
7. 警报发送邮箱，或 Auto Scaling操作， 或 EC2操作

  ![](https://i.loli.net/2019/06/15/5d04f7bcbb93621376.png)
  ![](https://i.loli.net/2019/06/15/5d04f7c161f9990762.png)
  ![](https://i.loli.net/2019/06/15/5d04f8484932581418.png)

8. 测试CPU
  ```
  [ec2-user@ip-172-31-28-183 ~]$ sudo -i
  [root@ip-172-31-28-183 ~]# while true; do echo "hi"; done
  hi
  hi
  hi
  ```
  ![](https://i.loli.net/2019/06/15/5d04fc717e6c870321.png)
  ![](https://i.loli.net/2019/06/15/5d04fc7648f9e84727.png)

# Exam Tips
![](https://i.loli.net/2019/06/15/5d04f1dc56b9d41582.png)
- **CloudTrail monitors API calls in the AWS platform**，因为任何一个账号创建EC2或是S3等资源时，都会想AWS发送一个API call

![](https://i.loli.net/2019/06/15/5d04fbd6dc8fb46372.png)

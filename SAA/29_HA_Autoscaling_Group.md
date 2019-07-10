# Autoscaling Group

[/SAA/07_Auto_Scaling.md](https://github.com/davidkorea/AWS/blob/master/SAA/07_Auto_Scaling.md)

## 1. Launch Configuration
- 伸缩组用来创建EC2实例的模板
- 已经创建不可更改
- 若伸缩组需要更改内部实例类型师，需要创建新的Launch Configuration，并关联至伸缩组
  ![](https://i.loli.net/2019/07/10/5d2597f40689d18392.png)
## ASG

- Group size: Start with `2` instances
  - The number of instances the group should have at any time, also called desired capacity

- Instances need: `300` seconds to warm up after scaling
  - This is the amount of time that your instances need to warm up. During this time, instances that have been launched will not contribute to the Auto Scaling group metrics. To learn more, visit our documentation.

![](https://i.loli.net/2019/07/10/5d2599eae6bda12878.png)
![](https://i.loli.net/2019/07/10/5d2599f5caaad44553.png)
![](https://i.loli.net/2019/07/10/5d2599fa9cda924323.png)

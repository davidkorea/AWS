# 1. EBS 101


If you make periodic snapshots of a volume, the snapshots are incremental递增的, which means that only the blocks on the device that have changed after your last snapshot are saved in the new snapshot

Even though snapshots are saved incrementally, the snapshot deletion process is designed so that you need to **retainc持有 only the most recent snapshot in order to restore the volume**

-----

- **EBS is a persistent block storage automatically replicated within its AZ to protect from component failure**，同一个AZ内有备份
- **EBS is virtual disk, and volumes run on it.**，EBS是一个硬盘，里面包含root和其他卷分区volume
- **snapshot of EBS volume is stored in S3, but not visible in your account S3, in AWS's S3**
- **take a snapshot of root volume, should shutdown instance, although it can be on fly. non-root volume can snapshot on fly**
  - **扩展容量可以直接on fly，需要重新执行分区，否则扩展的容量无法使用**
- magnetic adj.	像磁铁的; 有磁性的
- ephemeral adj. 短暂的 （Instance Store）
- EBS: root volume snapshot canbe deleted when instance is runing?? 根卷的快照是公共快照，不能删除，也无需删除。
- Instance Store: templates stored in S3
- **讲义错误**，AMI只能通过快照创建，直接在volume上找不到创建AMI选项。但是可以直接在EC2上面直接创建AMI，而无需snapshot
- **用于创建AMI的快照，不能被删除**，Can I delete a snapshot of an EBS Volume that is used as the root device of a registered AMI? NO.
  ![]()
- **可以强制拆除非root EBS volume，但是root volume不能再实例运行中强制拆除**
-----

![](https://i.loli.net/2019/06/15/5d049f6d89bc291836.png)

- API names need to know going into exams
- EBS is always in the same AZ as your EC2 created
- EC2创建时，root volume只有3中EBS类型，且~~默认不能加密~~**好像可以选择加密 AWS Linux**，默认跟随EC2删除时自动删除
  ![](https://i.loli.net/2019/06/15/5d04a26a46d6658295.png)
  ![](https://i.loli.net/2019/06/15/5d04a2e2b002e32022.png)
  
- EC2创建时，增加EBS时，可以选择5种类型，且可以选择加密
  ![](https://i.loli.net/2019/06/15/5d04a26a5199917336.png)

- 可以在EC2运行时，无需关机，直接修改root volume的EBS类型，大小
  - **更改容量后，EC2中需要re-partioning，重新分区，否则新增部分无法使用**
  - **卷容量只能增加，不能减少**
  ![](https://i.loli.net/2019/06/24/5d104043e384370675.png)
  ![](https://i.loli.net/2019/06/15/5d04a54f5d01227514.png)
  ![](https://i.loli.net/2019/06/15/5d04a6557abbf10969.png)
  ![](https://i.loli.net/2019/06/15/5d04a65a3cdbb46427.png)

# 2. Migrate EC2 
## 2.1 Migrate from a AZ to another AZ
1. create snapshot of current EC2's root volume
2. make a AMI by the root volume snapshot created above
    - **AMI can only be created by a snapshot of the volume rather that the EBS disk volume itself**
    - **虽然不能直接在volume上创建AMI，但是可以在EC2上直接创建AMI，运行或停止状态都可以**
3. lanuch a new EC2 by the AMI, select the another AZ in steps within the VPC

  ![](https://i.loli.net/2019/06/15/5d04a8746e38e79790.png)
  ![](https://i.loli.net/2019/06/15/5d04a8794276696828.png)
  ![](https://i.loli.net/2019/06/15/5d04a8b1945e132139.png)
## 2.2 Migrate from a Region to another Region
1. following the procedure above and create a AMI by this instance's root volume
2. copy the AMI to another Region
  ![](https://i.loli.net/2019/06/15/5d04aa24a55e384669.png)
  ![](https://i.loli.net/2019/06/15/5d04aa28a64b386048.png)

# 3. Instance Store

- 创建实例存储，无法在EC2 - Elastic Block Volume菜单中看到root volume
- 实例存储的EC2，不可以STOP，只能Reboot和Terminate，**重启不会删除文件**
- **添加root之外的实例存储，只能在创建EC2时添加。创建好实例后，不能添加实例存储，只能添加EBS**

  ![](https://i.loli.net/2019/06/15/5d04b165e61ca85313.png)
  ![](https://i.loli.net/2019/06/15/5d04b16b7f7fa15880.png)
  ![](https://i.loli.net/2019/06/15/5d04b170ba48096652.png)


# 4. 加密根卷 - 目前已经可以在创建EC2时直接加密根卷
- **虽然创建EC2时，可以对root volume加密。但是如果选择不加密，那么创建好EC2后，不能再进行加密了，Non-root卷也是如此**
  - **需要创建一个快照，并不支持加密选项**
  - **将此快照复制一份到相同region，此处支持加密**
  - **根据复制的加密快照，创建AMI，再创建EC2即可**
    ![](https://i.loli.net/2019/06/24/5d104a340336331923.png)

![](https://i.loli.net/2019/06/15/5d04e7f9aeb1610554.png)



# Exam Tips
![](https://i.loli.net/2019/06/15/5d04ac9b376b225607.png)
![](https://i.loli.net/2019/06/15/5d04ac9e1501f14156.png)
![](https://i.loli.net/2019/06/15/5d04aca1e346968600.png)
![](https://i.loli.net/2019/06/15/5d04b175a161342740.png)
![](https://i.loli.net/2019/06/15/5d04e7f564c0840150.png)
  - 将自己创建的AMI设置为公有
    ![](https://i.loli.net/2019/06/15/5d04e9894e62026752.png)
    ![](https://i.loli.net/2019/06/15/5d04e9c98e9c299110.png)

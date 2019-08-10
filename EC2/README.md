# EC2

- **stop， restart EC2 instance will not lose Private IPv4.**
    ![](https://i.loli.net/2019/08/10/fDc3owzHSvWJByP.png)
    
- Placement Group
    - Cluster, SAME AZ and SAME Hardware
        - NOt all types of EC2 can be placed in a Cluster placement group, eg t2.micra cannot
    - Partition, SAME AZ but different Hardware, max 7 Partition(hardware/host)
    - Spread, different AZ, and each AZ has max 7 instance 

- Stop行为=Terminate & 启用终止保护 
    - 仍然可以通过SSH到该EC2，执行`shutdown now`命令，从而将EC2终止掉
    - 通过console到关闭功能无法终止
        ![](https://i.loli.net/2019/08/10/IiGCw4LZm5kOVRS.png)

# Deploy a static web on S3 
https://www.youtube.com/watch?v=D6qB7MEFOe0

1. S3 bucket, public access, website
2. CloudFront， Certificate Manager
3. Route53


# 1. S3
## 1.1 创建S3 bucket
- 名称：可以写域名，可以以写别的
- Region：弗吉尼亚，或者首尔，但是申请ssl证书时要手动改为弗吉尼亚
- 创建完成后，上传本地静态网页至S3 bucket

## 1.2 权限
- 取消禁用public访问
- 存储桶策略
  ```json
  {
    "Version":"2012-10-17",
    "Statement":[{
    "Sid":"PublicReadGetObject",
          "Effect":"Allow",
      "Principal": "*",
        "Action":["s3:GetObject"],
        "Resource":["arn:aws:s3:::example-bucket/*"
        ]
      }
    ]
  }
  ```
  - 官方模版：[网站访问所需的权限](https://docs.aws.amazon.com/zh_cn/AmazonS3/latest/dev/WebsiteAccessPermissionsReqd.html)
## 1.3 静态网站托管属性
- 指定已上传的静态网页名称
- 测试终端节点： http://www.....com.s3-website-us-east-1.amazonaws.com 

![](https://i.loli.net/2019/05/24/5ce7a22481cae57232.png)

# 2. CloudFront
## 2.1 创建CloudFront
如图，不能直接选择下拉菜单的url，需要手动复制S3静态网站的地址，其他选项默认即可完成创建
- 先选择下拉菜单里面的源域名，会自动生成源ID，再手动更改源域名。否则源ID无法自动生成。当然这个ID也可以手动更改成其他的
![](https://i.loli.net/2019/05/24/5ce7a302ce53685732.png)
## 2.2 使用私有域名绑定S3网站
- 点击CloudFront条目的ID，进入常规选项卡，添加编辑
- 添加备用域名，【使用 ACM 请求或导入证书】
  - **！！！！！弗吉尼亚地区！！！！！**
  - 自定义ssl认证是灰色的，不可点击，需要在弗吉尼亚地区注册ssl证书，过10mins左右，可以点选
  ![](https://i.loli.net/2019/05/24/5ce7a6d79bfb538186.png)
  - 点击按钮【在Route53中创建记录】将证书记录添加至Route53，完成认证 
- 完成自动一域名后，还需等待CloudFront完成部署

## 2.3 配置缓存失效，一遍内容快速更新
因为CDN会复制内容至全球各个节点，这个内容是静态的，如果变动了之后，这些节点依然保存着之前的内容。导致访问时无法得到最新版本


# 3. Route53
![](https://i.loli.net/2019/05/24/5ce7a7dbd02e918185.png)
- 关联CloudFront 的域名至私有域名，类型为IPv4
- 当CloudFront显示部署完成时，即可访问私有域名


# Issues
#### 1. Unable to select Custom SSL Certificate 

https://stackoverflow.com/questions/28609262/unable-to-select-custom-ssl-certificate-stored-in-aws-iam

It took a whole day to AWS to propagate the new certificate to all of its nodes. 
Next day when I logged in to my AWS console, the certificate appeared in the dropdown and was enabled as well and I could configure distribution successfully.
Also, be sure to select us-east-1 (N. Virginia) when you make the certificate request; it's the only region that supports it at this time (even if your bucket / asset is in another region)

#### 2. 指定备用CNAME后，自定义ssl证书，还需要等待CloudFront Pending，才能访问
![](https://i.loli.net/2019/05/24/5ce794e7d214343217.png)
#### 3. 打开自定义域名后，显示xml页面
```xml
<Error>
    <Code>AccessDenied</Code>
    <Message>Access Denied</Message>
    <RequestId>89F25EB47DDA64D5</RequestId>
    <HostId>Z2xAduhEswbdBqTB/cgCggm/jVG24dPZjy1GScs9ak0w95rF4I0SnDnJrUKHHQC</HostId>
</Error>
```
CloudFront的源需要手动复制S3的静态网站url，不能在下啦菜单中选择

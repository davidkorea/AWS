# Deploy a static web on S3 
https://www.youtube.com/watch?v=D6qB7MEFOe0

1. S3 bucket, public access, website
2. 


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




# Issues
#### 1. Unable to select Custom SSL Certificate 

https://stackoverflow.com/questions/28609262/unable-to-select-custom-ssl-certificate-stored-in-aws-iam

It took a whole day to AWS to propagate the new certificate to all of its nodes. 
Next day when I logged in to my AWS console, the certificate appeared in the dropdown and was enabled as well and I could configure distribution successfully.
Also, be sure to select us-east-1 (N. Virginia) when you make the certificate request; it's the only region that supports it at this time (even if your bucket / asset is in another region)

#### 2. 指定备用CNAME后，自定义ssl证书，还需要等待CloudFront Pending，才能访问
![](https://i.loli.net/2019/05/24/5ce794e7d214343217.png)

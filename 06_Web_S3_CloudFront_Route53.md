# Deploy a static web on S3 
https://www.youtube.com/watch?v=D6qB7MEFOe0

### Unable to select Custom SSL Certificate 

https://stackoverflow.com/questions/28609262/unable-to-select-custom-ssl-certificate-stored-in-aws-iam

It took a whole day to AWS to propagate the new certificate to all of its nodes. 
Next day when I logged in to my AWS console, the certificate appeared in the dropdown and was enabled as well and I could configure distribution successfully.
Also, be sure to select us-east-1 (N. Virginia) when you make the certificate request; it's the only region that supports it at this time (even if your bucket / asset is in another region)

### 指定备用CNAME后，自定义ssl证书，还需要等待CloudFront Pending，才能访问
![](https://i.loli.net/2019/05/24/5ce794e7d214343217.png)

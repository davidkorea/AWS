
# HSM
## 1. create HSM cluster
![](http://tvax1.sinaimg.cn/large/006gDTsUgy1g9n1kis9fdj311v09iq3o.jpg)

## 2. init
![](http://tvax4.sinaimg.cn/large/006gDTsUgy1g9n1m18tm9j311x0ee407.jpg)

## 3. create HSM in cluster
![](http://tvax1.sinaimg.cn/large/006gDTsUgy1g9n1n1biv2j311x0cp3zp.jpg)
![](http://tva3.sinaimg.cn/large/006gDTsUgy1g9n1o9ubp7j311w0er75v.jpg)

## 4. download CSR & HSM
![](http://tvax1.sinaimg.cn/large/006gDTsUgy1g9n1vtz6suj311x0fo75n.jpg)

1. 签署sign CSR，[Initialize the Cluster - Sign the CSR](https://docs.aws.amazon.com/cloudhsm/latest/userguide/initialize-cluster.html)
  - download the CSR. 证书签名请求 `cluster-6sm3nqh6jlx_ClusterCsr.csr`
      ```
      -----BEGIN CERTIFICATE REQUEST-----
      MIIC0DCCAbgCAQAwgYoxRDAJBgNVBAYTAlVTMAkGA1UECAwCQ0EwDQYDVQQKDAZD
      E6R6Yw3EjssdPO1SJ+1eBO0tqRRgy7T6N7TYQIJNKgMgg40ad5c93RnJ4L/YB4cx
      E6R6Yw3EjssdPO1SJ+1eBO0tqRRgy7T6N7TYQIJNKgMgg40ad5c93RnJ4L/YB4cx
      E6R6Yw3EjssdPO1SJ+1eBO0tqRRgy7T6N7TYQIJNKgMgg40ad5c93RnJ4L/YB4cx
      MIIC0DCCAbgCAQAwgYoxRDAJBgNVBAYTAlVTMAkGA1UECAwCQ0EwDQYDVQQKDAZD
      E6R6Yw3EjssdPO1SJ+1eBO0tqRRgy7T6N7TYQIJNKgMgg40ad5c93RnJ4L/YB4cx
      E6R6Yw3EjssdPO1SJ+1eBO0tqRRgy7T6N7TYQIJNKgMgg40ad5c93RnJ4L/YB4cx
      E6R6Yw3EjssdPO1SJ+1eBO0tqRRgy7T6N7TYQIJNKgMgg40ad5c93RnJ4L/YB4cx
      E6R6Yw3EjssdPO1SJ+1eBO0tqRRgy7T6N7TYQIJNKgMgg40ad5c93RnJ4L/YB4cx
      MIIC0DCCAbgCAQAwgYoxRDAJBgNVBAYTAlVTMAkGA1UECAwCQ0EwDQYDVQQKDAZD
      E6R6Yw3EjssdPO1SJ+1eBO0tqRRgy7T6N7TYQIJNKgMgg40ad5c93RnJ4L/YB4cx
      E6R6Yw3EjssdPO1SJ+1eBO0tqRRgy7T6N7TYQIJNKgMgg40ad5c93RnJ4L/YB4cx
      E6R6Yw3EjssdPO1SJ+1eBO0tqRRgy7T6N7TYQIJNKgMgg40ad5c93RnJ4L/YB4cx
      095wc32DPe9hftTDHhMLbDefdWAQDFMg2DqBcqG3FdvprLAvmptxNLV9xF1fRx+K
      QZh04nBWoNbMvM3GwWq9m6PxLO50/q9sORVyMZZus6u44aaNMNGEdsHV+XK5sm+4
      fvtJ/A==
      -----END CERTIFICATE REQUEST-----
      ```
  - Create a private key.
  - Use the private key to create a signing certificate.
  - Sign your cluster CSR.
  
2. 验证群集的 HSM 的身份和真实性 (可选). 集群验证证书 `cluster-6sm3nqh6jlx_HsmCertificate.crt`
  ![](http://tva4.sinaimg.cn/large/006gDTsUgy1g9n20h3lkjj30d10gaq3e.jpg)

## 5. Upload Certs
![](http://tva4.sinaimg.cn/large/006gDTsUgy1g9n28xam2cj311w0fa0tw.jpg)












# Serverless Webpage with Lambda

## 1. Lambda IAM ROLE
- Lambda
- Policy： `AWSLambdaBasicExecutionRole`， 将日志上传到 CloudWatch 的权限。
  ![](https://i.loli.net/2019/07/15/5d2c367a820dc75620.png)

## 2. Lambda code
![](https://i.loli.net/2019/07/15/5d2c3ad2ad1f278741.png)

```html
<html>
  <script>
  function myFunction() {
      var xhttp = new XMLHttpRequest();
      xhttp.onreadystatechange = function() {
          if (this.readyState == 4 && this.status == 200) {
          document.getElementById("my-demo").innerHTML = this.responseText;
          }
      };
      xhttp.open("GET", "https://c88888888nt7.execute-api.ap-northeast-2.amazonaws.com/default/apigw", true);
      xhttp.send();
  }
  </script>

  <body>
    <div align="center"><br><br><br><br>
      <h1>Hello, <span id="my-demo">world wide cloud!</span></h1>
      <button onclick="myFunction()">Click me</button><br>
      <img src="https://s3.amazonaws.com/www.davidkorea.com/1.gif">
    </div>
  </body>
</html>

```
## 3. API Gateway

![](https://i.loli.net/2019/07/15/5d2c380a9fc1d84576.png)
![](https://i.loli.net/2019/07/15/5d2c380d3b3f448463.png)
![](https://i.loli.net/2019/07/15/5d2c38102002516448.png)
![](https://i.loli.net/2019/07/15/5d2c381493cf515281.png)
![](https://i.loli.net/2019/07/15/5d2c3816e305455755.png)

# 使用Lambda定时关闭和启动EC2实例

- 201906 full steps by capture images.
  https://github.com/davidkorea/AWS/blob/master/SAA/DOCS/lambda_start_stop_ec2.pptx

- [如何使用 Lambda 按固定间隔停止和启动 Amazon EC2 实例](https://aws.amazon.com/cn/premiumsupport/knowledge-center/start-stop-lambda-cloudwatch/)
  - IAM Role
    ```js
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "logs:CreateLogGroup",
            "logs:CreateLogStream",
            "logs:PutLogEvents"
          ],
          "Resource": "arn:aws:logs:*:*:*"
        },
        {
          "Effect": "Allow",
          "Action": [
            "ec2:Start*",
            "ec2:Stop*"
          ],
          "Resource": "*"
        }
      ]
    }
    ```
  - Stop ec2
    ```python
    import boto3
    region = 'us-west-1'
    instances = ['i-12345cb6de4f78g9h', 'i-08ce9b2d7eccf6d26']

    def lambda_handler(event, context):
        ec2 = boto3.client('ec2', region_name=region)
        ec2.stop_instances(InstanceIds=instances)
        print('stopped your instances: ' + str(instances))
    ```
  - Start ec2
    ```python
    import boto3
    region = 'us-west-1'
    instances = ['i-12345cb6de4f78g9h', 'i-08ce9b2d7eccf6d26']

    def lambda_handler(event, context):
        ec2 = boto3.client('ec2', region_name=region)
        ec2.start_instances(InstanceIds=instances)
        print('started your instances: ' + str(instances))

    ```
  - ERROR
    ```js
    ”errorMessage”: “2019-06-12T11:03:27.906Z 7fb8341d-e73f-4934-9caa-9a4b70b51eea Task timed out after 3.00 seconds”} 
    ```
    ![](https://i.loli.net/2019/06/15/5d047ad78178547766.png)
    
- [规则的计划表达式](https://docs.aws.amazon.com/zh_cn/AmazonCloudWatch/latest/events/ScheduledEvents.html)

# CloudTrail
![image](https://user-images.githubusercontent.com/26485327/70404967-01b48d00-1a7f-11ea-9879-45b8c0ac677d.png)



## 1. Management events
- read only
- write only
- all

![image](https://user-images.githubusercontent.com/26485327/70404494-9fa75800-1a7d-11ea-8e07-0272a2296db3.png)

## 2. Data events
- S3
  - read only
  - write only
  
![image](https://user-images.githubusercontent.com/26485327/70404562-d9785e80-1a7d-11ea-9e1f-29e5664a6654.png)

- Lambda

![image](https://user-images.githubusercontent.com/26485327/70404580-ec8b2e80-1a7d-11ea-9dce-bddcd758ef16.png)


## 3. Log files Integrity Validation

- CLI only, cannot enable on console
- `aws cloudtrail validate-logs `

  ```
  aws cloudtrail validate-logs --trail-arn arn:aws:cloudtrail:us-east-1:account-id:trail/my-trail --start-time 2018-01-01T00:00:00Z.
  ```

## 4. Trail logs Storage location
- create a new S3 bucket
- Don’t log data events on the bucket which is storing your CloudTrail logs. Doing so would create an **infinite loop**!

![image](https://user-images.githubusercontent.com/26485327/70404936-e9dd0900-1a7e-11ea-8e45-c437fb32b1e9.png)



## 5. Event history
- 90 days, by default

![image](https://user-images.githubusercontent.com/26485327/70404682-2e1bd980-1a7e-11ea-8aad-8be548efc162.png)

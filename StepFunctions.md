
You can use state machines to process long-running workflows. 

For example, if a customer orders a book and it requires several different events, use the state machine to run all the events. When the customer orders the book, 
- the state machine creates a credit card transaction
- generates a tracking number for the book
- notifies the warehouse to ship the order
- and then emails the tracking number to the customer

The AWS Step Functions service runs all these steps.

The benefit of AWS Step Functions is that it enables the compute to be stateless. The AWS Lambda functions and the Amazon EC2 instances provide compute to the state machine to execute in a stateless way. AWS Lambda functions and Amazon EC2 do not have to remember the information about the state of the current execution. The AWS Step Functions service remembers the information about the state of the current execution.

## Demo

![IMG_0034](https://user-images.githubusercontent.com/26485327/70974726-ca338980-20eb-11ea-94b9-e082457e6c71.jpeg)
![IMG_0035](https://user-images.githubusercontent.com/26485327/70974727-cbfd4d00-20eb-11ea-90e9-f89cb145d4ad.jpeg)
![IMG_0036](https://user-images.githubusercontent.com/26485327/70974730-cdc71080-20eb-11ea-9b6b-0576b68cafe1.jpeg)

## excution result
![IMG_0037](https://user-images.githubusercontent.com/26485327/70974738-cf90d400-20eb-11ea-8f03-3c12a901a1e3.jpeg)
![IMG_0038](https://user-images.githubusercontent.com/26485327/70974742-d0c20100-20eb-11ea-96a7-35645f8226ed.jpeg)

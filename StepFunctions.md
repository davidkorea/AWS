
You can use state machines to process long-running workflows. 

For example, if a customer orders a book and it requires several different events, use the state machine to run all the events. When the customer orders the book, 
- the state machine creates a credit card transaction
- generates a tracking number for the book
- notifies the warehouse to ship the order
- and then emails the tracking number to the customer

The AWS Step Functions service runs all these steps.

The benefit of AWS Step Functions is that it enables the compute to be stateless. The AWS Lambda functions and the Amazon EC2 instances provide compute to the state machine to execute in a stateless way. AWS Lambda functions and Amazon EC2 do not have to remember the information about the state of the current execution. The AWS Step Functions service remembers the information about the state of the current execution.

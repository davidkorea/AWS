

# --dry-run

```
aws ec2 run-instances --dry-run --image-id $(AMI) --instance-type t2.micro

// request would have succeeded, but DryRun flag is set
```

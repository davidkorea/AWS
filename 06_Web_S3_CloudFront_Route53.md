# Deploy a static web on S3 



It took a whole day to AWS to propagate the new certificate to all of its nodes. 
Next day when I logged in to my AWS console, the certificate appeared in the dropdown and was enabled as well and I could configure distribution successfully.
Also, be sure to select us-east-1 (N. Virginia) when you make the certificate request; it's the only region that supports it at this time (even if your bucket / asset is in another region)

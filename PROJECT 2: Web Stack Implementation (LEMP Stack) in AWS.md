
## Error and Resolution 1
Failure to connect using 

ssh -i "projectreload.pem" ubuntu@ec2-54-147-3-6.compute-1.amazonaws.com

Resolution was to edit security group settings to include access from anywhere 0.0.0.0/0

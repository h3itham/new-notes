- Integration hostinger with ALB 
	- Certificate 
		- Create certificate manually in AWS. (Certificate must be in `us-east-1`), After creating it you should move it to region which `ALB` working in. 
		- Validate certificate with `cname`, anc `value` in hostinger 



- To Serve chat and notification with AWS ALB you should enable `stickiness`
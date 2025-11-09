There are three use cases for cloudbuild [GithubRepo](https://github.vodafone.com/VFGroup-NextGenBI/MC2-cloud-build) 
1. Composer 
	- When data engineer uplodad dag into github repo, Cloudbuild 
		- Code Linting 
		- Upload dag into composer s3 bucket. 
2. Datafusion 
3. Collibra 

draft
there are three use cases for cloudbuild for mc2
1. When Data engineer upload dag into github repo it will trigger cloudbuild to 
	1. lint code 
	2. upload dag to composer s3 bucket  
2. CDF 
3. Collibra 
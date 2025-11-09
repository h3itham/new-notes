#### Reference Architecture 
![[cloudfrontstaticwebsite.png]]
- You can replace Amazon Route53 with any different Domain provider. 
- Define Region which you will work on in all resouces. 
#### Create S3 bucket 
- Differentiat if the application need build or not (React)
- Uncheck block puplic access. 
-  Enable `static website hosing`
	- From properities tab 
	- At the end of the page. 
#### Request Certificate from certificate manager. 
- Note that every certificates in AWS should be created at this region `us-east-1`
- If you don't use Route53 use `cname` and `cname-value` to activate this certificate. 
- From `CertificateManager` 
- Create new record in your domain provider with `cname` and `cname value`

#### CloudFront 
- in origin put s3 url on it, from `properities` at the end of the page you will find `bucket endpoint`
- Enable `HTTP` in origin to allow cloudfront to work with s3.  <span style="color:rgb(255, 0, 0)">IMP</span>
- For Single Page Application (SPA) 
	There is routing issue when deploying React Apps especially with tanStack Router, React Route 
	the problem is when you visit `https://canary.teslm.shop/delivery-companies` your browser requests `/delivery-companies` directly from s3 and s3 reponed with `Not Found` because this file is not in s3. And what happens when using local server it retrun back to index.html. 
	**Fix**: ==configure custom error response== 
	1. Open Cloudfront distribution. 
	2. Go to error Pages (create custom error response)
		1. Http error code: 404 
		2. Customize erro response: Yes 
		3. Response page path `/index.html`
		4. HTTP Response code: 200 
		5. (Optional) Do the same for `403` just in case. 
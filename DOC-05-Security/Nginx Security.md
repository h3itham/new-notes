- Basic Nginx security 
	```txt 
	add_header X-Content-Type-Options "nosniff";
	add_header X-Frame-Options "SAMEORIGIN";
	add_header X-XSS-Protection "1; mode=block";
	add_header Content-Security-Policy "default-src 'self';";
	```
	1. Prevents browsers from guessing MIME types, reducing security risks.
	2. Blocks clickjacking by allowing iframes only from the same site.
	3. Enables browser XSS filtering and blocks malicious scripts.
	4. Restricts content loading to the same domain, preventing XSS attacks.


 
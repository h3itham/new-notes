#### Installation & initializations 
1. install pass
	```bash 
	brew install pass gnupg
	```
2. Generate a GPG key 
	```bash 
	gpg --full-generate-key
	```
	- Type: `RSA and RSA`
	- Size: `4096`
	- Expiry: `0` (never)
	- Enter your name & email 

3.  List you key ID 
	```bash
	gpg --list-secret-keys --keyid-fromat=long 
	```
	You will see something like: `sec   rsa4096/ABCD1234EFGH5678`
    Copy `ABCD1234EFGH5678`.
	
4. initialize `pass`
	```bash 
	pass init <ID> 
	```
#### Initialize git 
```bash 
cd ~/.password-store
git init
git remote add origin git@github.com:yourname/passwords.git
git push -u origin master
```
#### Daily command 
1. Store password 
	```bash 
	pass inset aws/prod/root 
	```
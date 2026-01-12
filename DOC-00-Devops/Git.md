#### Introduction 
This notes are used to master git, and to be the best one at vodafone how work with git. 

#### Which file you contribute in 
1. git balm 
	```bash 
	git blam project/path 
	```
2. You can use your commit number to search in git 
	```txt 
	https://github.vodafone.com/VFGroup-CloudAnalytics/neuron-gcp-project-configs/commit/8b8916f3536
	```

#### To revert back to specific file and specific commit 
1. List all commit in this file. 
	```bash 
	git log -- [flieName]
	```
2. Checkout to this file on specific commit 
	```bash 
	git checkout [commitNumber] -- [fileNma]
	```
3. Commit your change and push it to remote rebo 
	```bash 
	git add . 
	git commit -m ""
	git push 
	```

#### Branch Protection Strategy. 
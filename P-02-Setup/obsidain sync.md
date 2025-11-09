1. Add files 
	```bash 
	git add . 
	```
2. Commit them 
	```bash 
	git commit -m "Regular sync"
	```
3. Move them to your machine 
	```bash 
	rsync -av --delete ../new-notes/ blue@62.171.171.26:/home/blue/new-notes
	```
4. Push from your machine to git 
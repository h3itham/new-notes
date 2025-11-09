1. You accidentally committed sensitive credentials to the Git repository. How would you remove them from the entire history?
	- You can remove specific file with `filter-repo` 
	- Reproduce issue 
		```bash 
		mkdir git-secrets-demo && cd git-secrets-demo
		git init
		
		# Simulate a secrets file
		echo "AWS_SECRET_KEY=abc123456" > secrets.env
		git add secrets.env
		git commit -m "Add sensitive file by mistake"
		
		# Simulate more commits after the mistake
		echo "App Version 1.0" > README.md
		git add README.md
		git commit -m "Add readme"
		```

	- Solution 
		```bash 
		# Make sure you have git-filter-repo installed
		pip install git-filter-repo
		
		# Remove the secrets file from all commits
		git filter-repo --path secrets.env --invert-paths
		git push origin --force --all
        git push origin --force --tags
		```

2. A Developer accidentally delete branch can you recover it? 
	Yes I can with "reflog" command 
	- Create Repo 
		```bash 
		mkdir branch-recovery-demo && cd branch-recovery-demo
		git init
		echo "base" > app.txt
		git add . && git commit -m "init commit"
		```
	- Create a branch and make commits 
		```bash 
		git checkout -b feature-x
		echo "Feature A" >> app.txt
		git commit -am "Add feature A"
		
		echo "Feature B" >> app.txt
		git commit -am "Add feature B"
		```
	- check log 
		```bash 
		git log --oneline 
		```
	- Accidentally Delete branch 
		```bash 
		git checkout main
		git branch -D feature-x  # Branch gone!
		```
	- Recovery Steps 
		```bash 
		# List all logs 
		git reflog 
		# Switch to specific commit 
		git checkout -b feature-x [COMMIT-HASH]
		```

3.Git Conflicts 
```bash 
<<<<<<Head 
YOUR CHANGES
===========
REMOTE CHANGES
>>>>>> 
```


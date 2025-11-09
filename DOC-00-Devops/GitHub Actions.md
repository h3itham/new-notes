
#### Used for 
1. Automate workflow (Build, Test, Deploy). 
2. Trigger Jobs 
3. Run scripts 
#### Workflow structure 
- Every workflows created in the following directory on the root folder 
	```bash 
	.github/workflows/workflow-name.yml
	```
- Example 
	```yaml 
	name: CI Pipeline # NAME  
	
	on: [push] # TRIGGER 
	
	jobs: # LOGICAL GROUP OF STEPS. 
	  build:
	    runs-on: ubuntu-latest #  WAHT TYPE MACHINE JOB RUN ON 
	    steps: # TASKS
	      - name: Checkout repository
	        uses: actions/checkout@v4
	
	      - name: Setup Node
	        uses: actions/setup-node@v4
	        with:
	          node-version: 20
	
	      - name: Install dependencies
	        run: npm install
	
	      - name: Run tests
	        run: npm test
	
	```

### Install Ollama 
1. From the official script 
	```bash 
	curl -fsSL https://ollama.com/install.sh | sh
	```
2. Start Ollama service 
	```bash 
	sudo systemctl start ollama
	sudo systemctl enable ollama
	```

3. test Ollama 
	```bash 
	ollama list 
	```
#### Expose Ollama 
1. Ollama list to the following port `127.0.0.1:11434`
2. 
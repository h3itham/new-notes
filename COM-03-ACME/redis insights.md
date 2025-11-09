Redis Insights installed with helm chart I will configure it know 

Add repo 
```bash 
 helm repo add heywood8 https://heywood8.github.io/helm-charts
```

Update helm 
```bash 
helm repo update 
```

Install it in specific name name space `redis-insights` 
- Show default values 
  ```bash
    helm show values heywood8/redisinsight > values.yaml
	```

- You can change any thing you want. I changed the pvc specs. 
- generate template from it which will be installed in `reddis-insights` namesapce 
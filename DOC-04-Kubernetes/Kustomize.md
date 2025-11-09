#### Introduction 
- Is powerful tool for managing kubernetes configurations. 
- Manage k8s manifests in Declarative way. Enable you to 
	- Reuse base configurations. 
	- Customize configuration for different environments (Dev, prod, and stage)
	- Avoid duplication 
- Kustomize is now integrated with `kubectl` via `kubectl apply -k `

##### Core Concepts of Kustomize.
- **Base** a directory containing the base kubernetes manifests (deployment, service, and configmap). 
- **Overlay** a directory that contains customization (environment specific changes ) 
- **kustomization.yaml** the configuration file that defines how kustomize should process the resources. It’s required in both base and overlay directories. 
- **Patches** Changes applied to the base manifests
- **Generators**: Used to generate Kubernetes resources (e.g., ConfigMaps, Secrets) dynamically.
- **Transformers**: Used to modify resources (e.g., adding labels, annotations, or name prefixes).

#### Demo 



#### Draft 
- why you should learn kustomize 
	- Simplifies configuration. 
	- Multiple Environment. 
	- Easy to use. 
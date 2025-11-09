#### Introduction 

<span style='color:red'>What is service mesh?</span> 
is an <span style='color:cyan'>Infrastructure Layer</span> that allow you to manage communication between the services of an application.  
Istio is service mesh which provide 
- Traffic Management. 
- Observability. 
- Security. 

Istio use a proxy to intercept all network traffic, allowing a board set of application-aware feature based on configuration you set. 
![](istio-architecture.png)
#### Traffic Management Concepts 
##### Virtual Service 
- Istio Configuration which define how traffic is routed to different k8s services. 
- Keys Concepts 
		1. Routing rules. 
		2. Traffic shifting 
		3. Fault Injection. 
		4. Retries & timeout 
		5. Destination Rules integration 
	
- Example 
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: example-virtualservice
  namespace: default
spec:
  hosts:
  - example.com
  http:
  - match:
    - uri:
        prefix: "/v1"
    route:
    - destination:
        host: example-service
        subset: v1
  - match:
    - uri:
        prefix: "/v2"
    route:
    - destination:
        host: example-service
        subset: v2
```

In this example: 
- Traffic to `example.com/v1`will be routed to `example-servcie:v1`
- Traffic to `example.com/v2` will be routed to `example-servcie:v`

#### Istio Installation 
1. Download istio
   ```bash 
	curl -L https://istio.io/downloadIstio | sh -
	cd  cd istio-1.23.2
	```
2. Add istio binary in PATH 
	```bash
	export PATH=$PATH:$PWD/bin
    ```


3. Install Istio 
	```bash 
	istioctl install 
	```

	This will deploy two pods `istiod` and `istio-ingress`
4. Install metrics server and the certificate 
	```bash
	kubectl apply -f https://raw.githubusercontent.com/alex1989hu/kubelet-serving-cert-approver/main/deploy/standalone-install.yaml

	kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml	
	```

    Note that metric server some times need add tls 
	```bash 
	wget -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
	# Note that you need to disable tls add the following to yaml file 
	# - --kubelet-insecure-tls
	```

5. Instruct Istio to automatically inject Envoy sidecar proxies when you deploy you application later in specific name sapce 
	```bash 
	kubectl lable namespace [NameSpace] istio-injection=enabled
	```

 6. Install Istio add-ons 
    ```bash 
    kubectl apply -f samples/addons/
	```
    One of the addons which is very important is Kiali, But it need prometheus to be installed first. 
    ```bash 
    kubectl apply -f https://raw.githubusercontent.com/istio/istio/master/samples/addons/prometheus.yaml -n istio-system
	```


#### Scenario 
I choose `Bookinfo` application which is provided with istio itself. 
We have Four Micros service in the bookinfo application

1. **Productpage:** The application's frontend service. it calls the details and reviews services to display the page content. 
2. **Details:** the service which contain the book details. it dose not call any other service. 
3. **reviews:** the service that contains the book reviews. it calls the ratings service to get the ratings. 
4. **Ratings:** the service that contains the review ratings. it dose not call any other service. 

![](bookinfo-arch.png )


- Enable automatic injection in default namespace 
  ```bash 
    kubectl label namespace default istio-injection=enabled
    ```

- Install bookinfo application 
  ```bash 
	kubectl apply  -f samples/bookinfo/platform/kube/bookinfo.yaml
	```

- All bookinfo related yaml files 
  ```txt
   samples/bookinfo
   ```
   After that you will see Each pod now has an Istio sidecar ⛵ that will intercept incoming and outgoing requests.
- test installation 
  ```bash 
	istioctl analyze 
	istioctl proxy-status 
	```

- Install all add-ons 
  ```bash
    kubectl apply -f samples/addon 
	```

- Create Gateway for application 
  ```bash
     kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
	```

- After that you can open the application "productpage" it self from the browser if you have metalLB running. 
  ```txt
    <IP>/productpage
	```

- Generate Traffic against it to activate graph 
  ```bash 
	watch -n 1 curl -s http://10.0.0.66/productpage -I
	```

### Traffic Management 
#### IstioGatway 

- Manage External HTTP, HTTPs and TCP traffic. 
- Dose not work without virtual service 
#### VirtualService 
- Istio Configuration which define how traffic is routed to different k8s services. 
- Use in Traffic Management and Control. 
- Set of routing rules for traffic coming from ingress gateway into the service mesh. 
#### Destination Rule 
* Defined <span style='color:green'>Policies</span> applied to traffic for specific servcie after routing has occur. 
* Applied after routing phase

<span style='color:red'> Recall Senario</span> 
- Delete the old Gateway 
  ```bash 
    kubectl delete -f samples/bookinfo/networking/bookinfo-gateway.yaml
	```

- Apply Custom Gateway 
  ```yml
	# custom-gateway.yaml
	apiVersion: networking.istio.io/v1beta1
	kind: Gateway
	metadata:
	  name: bookinfo-gateway
	spec:
	  selector:
	    istio: ingressgateway # Use the default ingress gateway or specify your custom gateway
	  servers:
	    - port:
	        number: 80
	        name: http
	        protocol: HTTP
	      hosts:
	        - "bookinfo.local"
	```

- Then Apply Virtual Service 
  ```yaml
    # custom-virtualservcie.yml
	apiVersion: networking.istio.io/v1alpha3
	kind: VirtualService
	metadata:
	  name: bookinfo
	spec:
	  hosts:
	    - bookinfo.local
	  gateways:
	    - bookinfo-gateway
	  http:
	    - match:
	        - uri:
	            exact: /productpage
	        - uri:
	            prefix: /static
	        - uri:
	            exact: /login
	        - uri:
	            exact: /logout
	        - uri:
	            prefix: /api/v1/products
	      route:
	        - destination:
	            host: productpage
	            port:
	              number: 9080
	```

- Apply the existing destination rules 
  ```bash
    kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml
	```

- Apply VirtualService to controll traffic 
  ```yaml
    # traffic-virtualservcie.yml
	apiVersion: networking.istio.io/v1beta1
	kind: VirtualService
	metadata:
	  name: reviews
	spec:
	  hosts:
	  - reviews
	  http:
	  - route:
	    - destination:
	        host: reviews
	        subset: v1
	      weight: 75
	    - destination:
	        host: reviews
	        subset: v2
	      weight: 25
	```

#### Fault Injection 
- Feature in istio which allow you simulate failure in microservcie. 
- Configured through virtualservice objects. 
- Ensure that your system can handle fault gracefully without crashing.
- Types 
	 1. Delays: introduce artificial delays in request. 
	 2. Abort: simulate Http error response. such as 404 and 500
	     Abort: mean terminate request before it complete. 
- Configured in virtualservice. 

#### Retries 
- Automatically re-send request to service when initial request fails. 
- Example 
  ```yaml
	apiVersion: networking.istio.io/v1alpha3
	kind: VirtualService
	metadata:
	  name: my-service
	spec:
	  hosts:
	    - my-service.example.com
	  http:
	    - retries:
	        attempts: 3
	        perTryTimeout: 1s
	        retryOn: "5xx,connect-failure,refused-stream"
	      route:
	        - destination:
	            host: my-service
	            port:
	              number: 8080
	```

#### Security in Istio. 



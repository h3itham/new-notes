1. Build scalable backend (Websocket scaling). 
	- The problem raise when we have multiple instance from our application, if the client disconnect and reconnect. it may forward to another instance (which doesn't know client) - `session affinity(sticky session)`
	- Multiple nodes [Socket.io Scaling](https://socket.io/docs/v3/using-multiple-nodes/)
2. Regarding (websockets) 
	- Why we use subdomain `canary-chat.teslm.shop` we don't use `Path-Based Routing` to be teslm.shop/chat  
Any updates 
Is that applicaable to erplace subdomain canary-chat.teslm.shop with Path-based Routing to be teslm.shop/chat
# README

An example of GO microservices application deployed on Kubernetes using minikube, Redis and RabbitMQ. 

1. A product mock 'database' that is on port 8081
..* export PRODUCT_URL=http://localhost:8081
2. Then a service that consumes this products and show them on a catalog that is running on port 8080 with 2 views:
⋅⋅* Catalog has a html basic global view .
 ⋅⋅* Catalog has a html basic product view .
3. Then with the product ID we can initiate a checkout service that will send the request to an exchange of RabbitMQ `checkout_ex`
⋅⋅1. Checkout has a Queue 
..2. Locally it used docker-compose to run RabbitMQ
..3. Setup the local ENV Variables for the service QUEUE
```export RABBITMQ_DEFAULT_USER="rabbitmq"
export RABBITMQ_DEFAULT_PASS="rabbitmq"
export RABBITMQ_DEFAULT_HOST=localhost  
export RABBITMQ_DEFAULT_PORT="5672"
export RABBITMQ_DEFAULT_VHOST="/" 
export RABBITMQ_CONSUMER_QUEUE="checkout_queue"
export PRODUCT_URL=http://localhost:8081
```  
..4. Checkout is running on port 8082
4. Then another service is created to process an Order in the same approacht that we used with Checkout with an exchange and queue named after the service. 
..1. run with `go run order.go -opt checkout`  or `go run order.go -opt payment` 
5. Then created a Payment in the same way with a exchange and a queue. 
..1. Remember to pass ENV variables to this service
``` 
export RABBITMQ_DEFAULT_USER="rabbitmq"
export RABBITMQ_DEFAULT_PASS="rabbitmq"
export RABBITMQ_DEFAULT_HOST=localhost  
export RABBITMQ_DEFAULT_PORT="5672"
export RABBITMQ_DEFAULT_VHOST="/" 
export RABBITMQ_CONSUMER_QUEUE="checkout_queue"
export PRODUCT_URL=http://localhost:8081
export REDIS_HOST="localhost:6379"
``` 
..2. Created Dockerfile to publis the microservices

5. Create k8s folder and setup files with all the images on Dockerhub 
6. Install [Minikube](https://minikube.sigs.k8s.io/docs/start/) 
..1.  brew install minikube
..2.  minikube start
..3.  minikube dashboard
7. Launch PODS
..1. kubectl apply -f redis.yaml
```
deployment.apps/redis created
service/redis-service created
```
..2. kubectl apply -f rabbitmq.yaml
..3. kubectl get svc
..4. minikube  service rabbitmq-service
..5. access with user & psw
..6. run remaining yaml files
..7. Create exchanges on rabbitmq `checkout_ex order_ex payment_ex` and bind them with the corresponding queues

I encountered problems installing minikube: [gist](https://gist.github.com/rahulkumar-aws/65e6fbe16cc71012cef997957a1530a3)

Phase 2. Monitoring the Service Mesh with [Istio](https://istio.io/latest/docs/)





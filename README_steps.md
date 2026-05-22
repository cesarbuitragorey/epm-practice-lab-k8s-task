

sudo apt update
sudo apt install docker-buildx -y
imagen:
docker build -t cesarbuitragorey_application:latest .
into applitacion folder
login docker:
docker login
docker tag cesarbuitragorey_application:latest cesarbuitrago/cesarbuit
ragorey_application:latest
docker push cesarbuitrago/cesarbuitragorey_application:latest
execute docker compuse: 
docker compose up -d
docker ps
hadolint Dockerfile
34  curl http://localhost:5000
 2035  curl -f http://localhost:5000/color
 2036  curl -f curl http://localhost:5000/test_db
 2037  curl http://localhost:5000/db_message
 minikube start
  2049  chmod +x ./utils/local_minikube_preparation.sh
 2050  ./utils/local_minikube_preparation.sh "Cesar Buitrago"
 kubectl config use-context minikube-cbuitrago
 kubectl get pods
  2055  kubectl --namespace default get pods
   2057  kubectl apply -f manifest.yml
   kubectl get pods
   kubectl get svc
   kubectl get ingress
 2063  minikube ip
 2064  ``
 2065  sudo nano /etc/hosts
 2066  sudo cat /etc/hosts
 minikube addons enable ingress
kubectl --namespace ingress-nginx get pods
curl http://cesarbuitragorey.application.com
docker tag cesarbuitrago/cesarbuitragorey_application:latest cbuitrago_application:latest

---
 2003  kubectl delete deployment application
 2004  kubectl delete svc application
 2005  kubectl apply -f manifest.yml
---
./final-check -test-suite 'final_task' -student "Cesar Buitrago"
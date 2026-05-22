# Kubernetes Practice Lab 🚀

## Objetivo
Desplegar una aplicación Flask con MongoDB en Kubernetes, usando:
- ConfigMaps
- Secrets
- StatefulSets
- Ingress

## Flujo
Docker → Kubernetes → Ingress → Testing
---
epm-practice-lab-k8s-task/
│
├── application/                # Código fuente de la app (Flask)
│   ├── app.py
│   ├── requirements.txt
│   ├── static/
│   │   └── styles.css
│   ├── templates/
│   │   └── *.html
│   └── Dockerfile             # Imagen de la aplicación
│
├── mongo/                     # Configuración de MongoDB
│   ├── statefulset.yaml
│   ├── service.yaml
│   └── secret.yaml
│
├── k8s/                       # Manifiestos principales de Kubernetes
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── configmap.yaml
│   ├── ingress.yaml
│   ├── statefulset.yaml
│   └── manifest.yml           # (archivo consolidado que usaste)
│
├── utils/                     # Scripts auxiliares del lab
│   └── local_minikube_preparation.sh
│
├── docker-compose.yml         # Entorno local para pruebas
│
├── .env                       # Variables de entorno (si aplica)
│
├── final-check                # Script del evaluador (lab)
│
├── README.md                  # Documentación (lo que estás armando)
│
└── .gitignore
###############################
# 🔹 1. LIMPIEZA DEL ENTORNO
###############################

# Eliminar recursos Docker antiguos (libera espacio y evita conflictos)
docker system prune -af
docker volume prune -f

# Ajustar límites del sistema (necesario para apps con muchos eventos de archivos)
sudo sysctl fs.inotify.max_user_watches=524288
sudo sysctl fs.inotify.max_user_instances=8192

# Persistir configuración en el sistema
echo "fs.inotify.max_user_watches=524288" | sudo tee -a /etc/sysctl.conf
echo "fs.inotify.max_user_instances=8192" | sudo tee -a /etc/sysctl.conf

# Aplicar cambios inmediatamente
sudo sysctl -p


###############################
# 🔹 2. REINICIAR MINIKUBE
###############################

# Elimina cluster previo (limpio)
minikube delete -p minikube-cbuitrago

# Crear cluster nuevo con Docker driver
minikube start -p minikube-cbuitrago --driver=docker


###############################
# 🔹 3. CONTEXTO KUBERNETES
###############################

# Cambiar al contexto del cluster creado
kubectl config use-context minikube-cbuitrago


###############################
# 🔹 4. BUILD Y PUBLICACIÓN DOCKER
###############################

# Construir imagen de la app
docker build -t cesarbuitragorey_application:latest .

# Login a Docker Hub
docker login

# Etiquetar imagen para repositorio remoto
docker tag cesarbuitragorey_application:latest cesarbuitrago/cesarbuitragorey_application:latest

# Subir imagen al registry
docker push cesarbuitrago/cesarbuitragorey_application:latest


###############################
# 🔹 5. VALIDACIÓN LOCAL CON DOCKER-COMPOSE
###############################

# Levantar servicios localmente
docker compose up -d

# Ver contenedores activos
docker ps

# Probar endpoints localmente
curl http://localhost:5000
curl -f http://localhost:5000/color
curl http://localhost:5000/test_db
curl http://localhost:5000/db_message


###############################
# 🔹 6. LINT DEL DOCKERFILE
###############################

# Validar buenas prácticas del Dockerfile
hadolint Dockerfile


###############################
# 🔹 7. PREPARAR CLUSTER LOCAL
###############################

# Dar permisos al script
chmod +x ./utils/local_minikube_preparation.sh

# Crear namespace, usuario y contexto
./utils/local_minikube_preparation.sh "Cesar Buitrago"

# Usar el contexto generado
kubectl config use-context minikube-cbuitrago


###############################
# 🔹 8. DESPLEGAR EN K8S
###############################

# Aplicar manifiestos (deployment, service, configmap, ingress, etc)
kubectl apply -f manifest.yml

# Verificar recursos
kubectl get pods
kubectl get svc
kubectl get ingress
# acceder a la app y bd

kubectl port-forward svc/application 8080:80
http://localhost:8080/test_db
###############################
# 🔹 9. CONFIGURAR INGRESS
###############################

# Habilitar ingress controller
minikube addons enable ingress -p minikube-cbuitrago

# Verificar pods de ingress
kubectl get pods -n ingress-nginx


###############################
# 🔹 10. CONFIGURACIÓN DNS LOCAL
###############################

# Obtener IP del cluster
minikube ip -p minikube-cbuitrago

# IMPORTANTE:
# Agregar en Linux (WSL):
# sudo nano /etc/hosts
# 192.168.49.2 cbuitrago.application.com

# En Windows:
# C:\Windows\System32\drivers\etc\hosts
# 192.168.49.2 cbuitrago.application.com

# Limpiar cache DNS en Windows
# ipconfig /flushdns


###############################
# 🔹 11. TUNNEL PARA INGRESS
###############################

# Exponer servicios a red host (necesario en WSL/Docker)
minikube tunnel -p minikube-cbuitrago


###############################
# 🔹 12. PRUEBAS DE ACCESO
###############################

# Validar endpoint vía ingress
curl http://cbuitrago.application.com/test_db

# Insertar datos en Mongo (necesario para tests finales)
curl -X POST http://cbuitrago.application.com/test_db \
  -d "name=test&message=hello"

# Verificar datos en DB
curl http://cbuitrago.application.com/db_message


###############################
# 🔹 13. TROUBLESHOOTING
###############################

# Ver pods
kubectl get pods

# Describir pod
kubectl describe pod <nombre_pod>

# Logs de aplicación
kubectl logs <nombre_pod>

# Reiniciar pods para aplicar ConfigMap
kubectl delete pod -l app=application

# Ver ingress
kubectl get ingress
kubectl describe ingress nginx


###############################
# 🔹 14. RECREAR DEPLOYMENT
###############################

# Eliminar y recrear deployment
kubectl delete deployment application
kubectl delete svc application
kubectl apply -f manifest.yml


###############################
# 🔹 15. CHECK FINAL
###############################

# Ejecutar pruebas automáticas del laboratorio
./final-check -test-suite 'final_task' -student "Cesar Buitrago"
----


minikube start
minikube start -p minikube-cbuitrago --driver=docker
kubectl config use-context minikube-cbuitrago
``
docker system prune -af
docker volume prune -f
sudo sysctl fs.inotify.max_user_watches=524288
sudo sysctl fs.inotify.max_user_instances=8192
echo "fs.inotify.max_user_watches=524288" | sudo tee -a /etc/sysctl.conf
echo "fs.inotify.max_user_instances=8192" | sudo tee -a /etc/sysctl.conf

# Aplicar los cambios del archivo inmediatamente
sudo sysctl -p
# Elimina el contenedor e infraestructura rota de este perfil
minikube delete -p minikube-cbuitrago

# Vuelve a iniciar el clúster desde cero
minikube start -p minikube-cbuitrago --driver=docker

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
# troubleshooting 
kubectl get pods
kubectl describe pod application-7fb6b8c9dd-7twzd
kubectl logs application-7fb6b8c9dd-7twzd

kubectl delete pod -l app=application
kubectl get ingress
minikube ip -p minikube-cbuitrago
C:\Windows\System32\drivers\etc\hosts
192.168.49.X cbuitrago.application.com
ipconfig /flushdns
minikube tunnel -p minikube-cbuitrago
minikube addons enable ingress -p minikube-cbuitrago
kubectl get pods -n ingress-nginx
kubectl get ingress
kubectl describe ingress nginx
curl http://cbuitrago.application.com/test_db
curl -X POST http://cbuitrago.application.com/test_db \
  -d "name=test&message=hello"
curl http://cbuitrago.application.com/db_message
# checker
./final-check -test-suite 'final_task' -student "Cesar Buitrago"
#!/bin/bash
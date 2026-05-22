

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

---
./final-check -test-suite 'final_task' -student "Cesar Buitrago"
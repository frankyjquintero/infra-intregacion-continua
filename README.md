# infra-intregacion-continua

docker-compose up -d

docker-compose down

docker run -p 8080:8080 -p 50000:50000 -v C:\Ic\jenkins:/var/jenkins_home jenkins/jenkins

docker run -p 8081:9000 -v C:\Ic\sonarqube\conf:/opt/sonarqube/conf -v C:\Ic\sonarqube\data:/opt/sonarqube/data -v C:\Ic\sonarqube\extensions:/opt/sonarqube/extensions -v C:\Ic\sonarqube\bundled-plugins:/opt/sonarqube/lib/bundled-plugins sonarqube

Portainer

docker volume create portainer_data
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer

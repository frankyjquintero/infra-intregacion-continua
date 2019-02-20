# infra-intregacion-continua

docker-compose up -d

docker-compose down

docker run -p 8080:8080 -p 50000:50000 -v C:\Ic\jenkins:/var/jenkins_home jenkins/jenkins

docker run -d --name sonarqube -p 9000:9000 -v C:\Ic\sonarqube\conf:/opt/sonarqube/conf -v C:\Ic\sonarqube\data:/opt/sonarqube/data -v C:\Ic\sonarqube\extensions:/opt/sonarqube/extensions sonarqube

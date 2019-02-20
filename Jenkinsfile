def CONTAINER_NAME="jenkins-pipeline"
def CONTAINER_TAG="latest"
def DOCKER_HUB_USER="frankyjquintero"
def HTTP_PORT="8090"

node {
    stage('Get Code from Git') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/frankyjquintero/Inport-DDD.git'
  
   }
    stage('Nuget Pakages Restore') { // for display purposes
     //bat('dotnet restore /nodereuse:false')
   }
   stage('Test Xunit and Coverage') { // for display purposes
    bat('dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=opencover --logger "trx;LogFileName=unit_tests.xml\"')
    xunit([MSTest(deleteOutputFiles: true, failIfNotNew: false, pattern: '**/TestResults/unit_tests.xml', skipNoTestFiles: false, stopProcessingIfError: true)])

   }
   
    stage('QA Test') { // for display purposes
        withSonarQubeEnv('SonarQube') {
          bat('dotnet sonarscanner begin /k:"Inport" /d:sonar.host.url="http://127.0.0.1:9000" /d:sonar.login="5c7b6e6f3b5db293ff8396bd7271c8576a66bd33"')
    	  bat('dotnet build /nodereuse:false')
    	  bat('dotnet sonarscanner end /d:sonar.login="5c7b6e6f3b5db293ff8396bd7271c8576a66bd33"')   
        }
    }
    
    // No need to occupy a node
    stage("Quality Gate"){
        timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
            def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
            if (qg.status != 'OK') {
                error "Pipeline aborted due to quality gate failure: ${qg.status}"
            }
        }
    }
    
   stage('Build') {
      //bat('dotnet build /nodereuse:false')
   }
   stage("Image Prune"){
        imagePrune(CONTAINER_NAME)
    }
   stage('Build Image Docker') {
        imageBuild(CONTAINER_NAME, CONTAINER_TAG)
   }
   stage('Deploy Image Docker') {
      withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            pushToImage(CONTAINER_NAME, CONTAINER_TAG, USERNAME, PASSWORD)
        }
   }
   stage('Run App'){
        runApp(CONTAINER_NAME, CONTAINER_TAG, DOCKER_HUB_USER, HTTP_PORT)
    }
}


def imagePrune(containerName){
    try {
        bat("docker image prune -f")
        bat("docker stop $containerName")
    } catch(error){}
}

def imageBuild(containerName, tag){
    bat("docker build -t $containerName:$tag  -t $containerName --pull --no-cache .")
    echo "Image build complete"
}

def pushToImage(containerName, tag, dockerUser, dockerPassword){
    bat("docker login -u $dockerUser -p $dockerPassword")
    bat("docker tag $containerName:$tag $dockerUser/$containerName:$tag")
    bat("docker push $dockerUser/$containerName:$tag")
    echo "Image push complete"
}

def runApp(containerName, tag, dockerHubUser, httpPort){
    bat("docker pull $dockerHubUser/$containerName")
    bat("docker run -d --rm -p $httpPort:$httpPort --name $containerName $dockerHubUser/$containerName:$tag")
    echo "Application started on port: ${httpPort} (http)"
}

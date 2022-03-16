node {
    def mvnHome = tool 'Maven 3.6.2'

    def dockerImage
    
    def dockerRepoUrl = "localhost:8083"
    def dockerImageName = "hello-world-java"
    def dockerImageTag = "${dockerRepoUrl}/${dockerImageName}:${env.BUILD_NUMBER}"
    
    stage('Clone Repo') { 
      git 'https://github.com/JacobSk8/docker-hello-world-spring-boot-master.git'
      mvnHome = tool 'Maven 3.6.2'
    }    
  
    stage('Build Project') {
      sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
// 	 sh '${mvnHome}  clean package'
    }
	
	stage('Publish Tests Results'){
      parallel(
        publishJunitTestsResultsToJenkins: {
          echo "Publish junit Tests Results"
		  junit '**/target/surefire-reports/TEST-*.xml'
		  archive 'target/*.jar'
        },
        publishJunitTestsResultsToSonar: {
          echo "This is branch b"
      })
    }
		
    stage('Build Docker Image') {
	      environment {
                dockerHome = tool 'myDocker'
                DOCKER_HUB_LOGIN = credentials('docker-hub')
            }
      // build docker image
      sh "whoami"
//       sh "ls -all /var/run/docker.sock"
      sh "mv ./target/hello*.jar ./data" 
      
      dockerImage = 'docker.build("hello-world-java")'
    }
   
    stage('Deploy Docker Image'){
          environment {
                dockerHome = tool 'myDocker'
                DOCKER_HUB_LOGIN = credentials('docker-hub')
            }
      // deploy docker image to nexus

      echo "Docker Image Tag Name: ${dockerImageTag}"

      sh "${dockerHome} login -u admin -p admin123 ${dockerRepoUrl}"
      sh "${dockerHome} tag ${dockerImageName} ${dockerImageTag}"
      sh "${dockerHome} push ${dockerImageTag}"
    }
}

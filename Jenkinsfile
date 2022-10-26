node{
    
    def buildNumber = BUILD_NUMBER
    stage("Git Clone"){
        
        git url: 'https://github.com/Radhikavrv/Java-web-app-docker.git', branch: 'master'
    }
    
    stage("Maven Clean Package"){
        def mavenHome= tool name: "Maven",type: "maven"
         sh "${mavenHome}/bin/mvn clean package"
    
    }
    
    stage("Build Docker Image"){
         sh "docker build -t radhikavrv/java-web-app-docker:${buildNumber} ."
    }
    
    stage("Docker Login and Push"){
        withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]){
            sh "docker login -u radhikavrv -p ${DockerHubPwd}"
         }
         
        sh "docker push radhikavrv/java-web-app-docker:${buildNumber}"
    }

    	    stage("Shutting Down Docker Compose"){
        
        sshagent(['Docker_Dev_Server_SSH1']) {
          sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.4.238 docker-compose down"
        }
          
      }        
    
    
     withCredentials([sshUserPrivateKey(credentialsId: "Docker_Dev_Server_SSH1", keyFileVariable: 'keyfile')]) {

    	stage("docker-compose.yaml file copy"){
        
       
        sh "scp -i ${keyfile} docker-compose.yaml ubuntu@172.31.4.238:"  
    }
   }

   stage("Launching Containers using Docker compose method"){
        
        sshagent(['Docker_Dev_Server_SSH1']) {
          sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.4.238 docker-compose up -d"
        }
          
      }
    
}

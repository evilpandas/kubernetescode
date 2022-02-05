pipeline {
  

    
    environment {
      tag = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
    }
  
  stages {
  stage('Clone repository') {
      

        checkout scm
    }


    stage('Build image') {
      script {
         app = docker.build("evilpandas/jenkins-test")
      }
    }

  
    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }
        
    stage('Push image') {
      
      script {  
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${tag}")
          }
      }
    }
    
    stage('Trigger ManifestUpdate') {
      
        echo "triggering updatemanifestjob"
        build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: "${tag}")]
        }

}
}

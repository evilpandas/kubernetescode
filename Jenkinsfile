pipeline {
  agent any
  

    
    environment {
      tag = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
    }
  
  stages {

    stage('Clone repository') {
      steps {  
        checkout scm
      }
    }


    stage('Build image') {
      steps {
        script {
           app = docker.build("evilpandas/jenkins-test")
        }
      }
    }

  
    stage('Test image') {
  
      steps {
        script {
           app.inside {
              sh 'echo "Tests passed"'
           }
        }
      }
    }
        
    stage('Push image') {
      steps {
        script {  
            docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
              app.push("${tag}")
            }
         }
      }
    }
    
    stage('Trigger ManifestUpdate') {
      steps {
        echo "triggering updatemanifestjob"
        build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: "${tag}")]
        }
    }

}
}

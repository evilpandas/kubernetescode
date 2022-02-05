def app

pipeline {
  agent any

    
    checkout scm
    
    environment {
      tag = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
    }

/*    stage('Clone repository') {
      

        checkout scm
    }
*/
  stages {
    stage('Build image') {
  
       app = docker.build("evilpandas/jenkins-test")
    }

    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
        
        app.inside {
          sh 'printenv'
        }
    }

    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${tag}")
        }
    }
    
    stage('Trigger ManifestUpdate') {
                echo "triggering updatemanifestjob"
        build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: "${tag}")]
        }

  }
}

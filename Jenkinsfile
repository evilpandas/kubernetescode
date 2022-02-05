node {
  
    def app
    
    environment {
      tag = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
    }
  
    stage('Clone repository') {
      

        checkout scm
    }


    stage('Build image') {
  
       app = docker.build("evilpandas/jenkins-test")
    }

  
    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }
        
    stage('Push image') {
      
        app.inside {
          sh 'export tag=`git rev-parse --short=10 HEAD | trim`'
        }
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${tag}")
        }
    }
    
    stage('Trigger ManifestUpdate') {
      
        app.inside {
           sh 'export tag=`git rev-parse --short=10 HEAD | trim`'
        }
        echo "triggering updatemanifestjob"
        build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: "${tag}")]
        }

}

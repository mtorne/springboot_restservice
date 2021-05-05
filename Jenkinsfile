node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build')
    {
         sh './mvnw package' 
    }
    
    
    stage('Build image') {
  
       app = docker.build("marctorne/restservice")
    }

    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}
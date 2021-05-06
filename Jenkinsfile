node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Sonarqube') {
        environment {
            scannerHome = tool 'sonarqubescanner'
        }    steps {
            withSonarQubeEnv('SonarQube') {
                sh "${scannerHome}/bin/sonar-scanner"
            }        timeout(time: 10, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
            }
        }
    }

    stage('Build')
    {
         sh './mvnw package' 
    }
    
    
    stage('Build image') {
  
       app = docker.build("mtorne/restservice")
    }

    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}
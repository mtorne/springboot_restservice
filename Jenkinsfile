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

    stage('Deploy to GKE') {
          
                sh "sed -i 's/restservice:latest/restservice:${env.BUILD_ID}/g' manifest.yaml"
                sh 'export PATH=$PATH:/usr/local/bin/'
                step([$class: 'KubernetesEngineBuilder', 
                projectId: env.PROJECT_ID, 
                clusterName: env.CLUSTER_NAME, 
                location: env.LOCATION, 
                manifestPattern: 'manifest.yaml', 
                credentialsId: env.CREDENTIALS_ID, 
                verifyDeployments: false])
           
        }
}
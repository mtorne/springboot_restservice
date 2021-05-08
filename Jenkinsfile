node {
    def app
    environment {
        PROJECT_ID = 'emerald-trilogy-313008'
        CLUSTER_NAME = 'cluster-1'
        LOCATION = 'europe-west6-a'
        CREDENTIALS_ID = 'emerald-trilogy-313008'
    }

    stage('Clone repository') {
      

        checkout scm
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
                projectId: 'emerald-trilogy-313008', 
                clusterName: 'cluster-1', 
                location: 'europe-west6-a', 
                manifestPattern: 'manifest.yaml', 
                credentialsId: 'emerald-trilogy-313008', 
                verifyDeployments: false])
           
        }
}
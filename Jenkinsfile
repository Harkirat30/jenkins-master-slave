pipeline {
agent{
    kubernetes{
       inheritFrom 'jenkins-slave'
    }
}
    environment {
        ZONE = "us-central1-a"
        PROJECT_ID = "pod-x-harkirat-singh"
    }

    stages{
        
        stage('Checkout') {
            steps {
                // Checkout code from the public Git repository
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/main']],  // Replace 'main' with your branch name
                          userRemoteConfigs: [[url: 'https://github.com/Harkirat30/jenkins-master-slave.git']]])
                          
            }
        }
        stage("Building Application Docker Image"){
            steps{
                script{
                    sh 'gcloud auth configure-docker us-central1-docker.pkg.dev'
                    sh 'docker build . -t us-central1-docker.pkg.dev/pod-x-harkirat-singh/jenkins/hello-app:${BUILD_NUMBER}'
                    }
                }
            }
            
        stage('Update Deployment YAML') {
            steps {
                script {
                    sh '''
                    sed -i "s|{batman}|${BUILD_NUMBER}|g" deployment.yaml
                    '''
                }
            }
        }
        
        stage("Pushing Application Docker Image to Google Artifact Registry"){
            steps{
                script{
                    sh 'docker push us-central1-docker.pkg.dev/pod-x-harkirat-singh/jenkins/hello-app:${BUILD_NUMBER}'
        }}}
        stage("Application Deployment on Google Kubernetes Engine"){
            steps{
                script{
                    sh "gcloud container clusters get-credentials primary --zone ${env.ZONE} --project ${env.PROJECT_ID}"
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }
    }
    }

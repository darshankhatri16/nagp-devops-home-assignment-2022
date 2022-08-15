pipeline {
    agent any
    environment {
        PROJECT_ID = 'nagp-workshop-kubernetes'
        CLUSTER_NAME = 'nagp-kuberetes-batch'
        LOCATION = 'us-central1'
        CREDENTIALS_ID = 'kubernetes-config'
    }
    stages {
        stage("Checkout code") {
            steps {
                git branch: 'master', credentialsId: 'Git_Credentials', url: 'https://github.com/darshankhatri16/nagp-devops-home-assignment-2022'
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("khatridarshan16/i-khatridarshan16-develop:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/i-khatridarshan16-develop:latest/i-khatridarshan16-develop:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }
}

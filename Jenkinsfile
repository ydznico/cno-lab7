pipeline {
    agent any
    environment {
        IMAGE_NAME = "ydznico/cno-lab7-app:latest"
    }
    stages {
        stage("Checkout Code") {
            steps {
                checkout scm
            }
        }
        stage("Build Docker Image") {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }
        stage("Push to Docker Hub") {
            steps {
                withCredentials([usernamePassword(credentialsId: "dockerhub-creds", usernameVariable: "DOCKER_USER", passwordVariable: "DOCKER_PASS")]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push ${IMAGE_NAME}"
                }
            }
        }
        stage("Deploy to Kubernetes") {
            steps {
                sh "kubectl --insecure-skip-tls-verify apply -f k8s-lab7.yaml"
                sh "kubectl --insecure-skip-tls-verify rollout restart deployment/cno-lab7-jenkins-app"
                sh "kubectl --insecure-skip-tls-verify rollout status deployment/cno-lab7-jenkins-app"
            }
        }
    }
}

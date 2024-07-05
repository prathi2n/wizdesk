pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "prathi2n/wizdesk:latest"
        KUBECONFIG = credentials('kubeconfig-credentials-id')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-credential-id', url: 'https://github.com/prathi2n/wizdesk'
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials-id') {
                        docker.image(DOCKER_IMAGE).push()
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig([credentialsId: KUBECONFIG]) {
                        sh 'kubectl apply -f k8s/deployment.yaml'
                        sh 'kubectl apply -f k8s/service.yaml'
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}

pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = "shinykuchi/naa-chitti-lokam-webapp"
        DOCKER_CREDS = "docker-credentials"
        SONARQUBE_ENV = "SonarQube"
        SONAR_TOKEN = credentials('sonar-token')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shinydevlearn-2022/Naa-Chitti-Lokam.git'
            }
        }

        stage('Docker Build') {
            steps {
                echo "🛠️ Building Docker image..."
                script {
                    def dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
                }
            }
        }

        stage('Run Container (Test)') {
            steps {
                echo "🧪 Running temporary test container..."
                script {
                    sh 'docker run -d -p 3000:3000 --name naa-chitti-test ${DOCKER_HUB_REPO}:latest'
                }
            }
        }

        stage('Check Running Containers') {
            steps {
                sh 'docker ps -a | grep naa-chitti-test || true'
                sh 'docker port naa-chitti-test || true'
            }
        }

        stage('Docker Push') {
            steps {
                echo "📦 Pushing Docker image to DockerHub..."
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDS}") {
                        sh """
                        docker tag ${DOCKER_HUB_REPO}:latest index.docker.io/${DOCKER_HUB_REPO}:latest
                        docker push index.docker.io/${DOCKER_HUB_REPO}:latest
                        """
                    }
                }
            }
        }

        stage('Code Quality - SonarQube') {
            steps {
                echo "🔎 Running SonarQube code quality analysis..."
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    script {
                        sh """
                        ${tool 'SonarScanner'}/bin/sonar-scanner \
                          -Dsonar.projectKey=testproject \
                          -Dsonar.projectName=Naa-Chitti-Lokam \
                          -Dsonar.sources=. \
                          -Dsonar.host.url=http://localhost:9000 \
                          -Dsonar.login=${SONAR_TOKEN}
                        """
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "🚀 Deploying to Kubernetes..."
                script {
                    sh """
                    echo 'Checking Minikube status...'
                    minikube status || minikube start

                    echo 'Applying Kubernetes manifests...'
                    kubectl delete -f naa-chitti-deployment.yaml --ignore-not-found
                    kubectl apply -f naa-chitti-deployment.yaml

                    echo 'Checking deployed resources...'
                    kubectl get pods
                    kubectl get svc
                    kubectl get deployments
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ SUCCESS: Build, Scan, and Deploy completed successfully!"
        }
        failure {
            echo "❌ FAILURE: Check pipeline logs for details."
        }
        always {
            script {
                echo "🧹 Cleaning up test container..."
                sh 'docker rm -f naa-chitti-test || true'
            }
        }
    }
}

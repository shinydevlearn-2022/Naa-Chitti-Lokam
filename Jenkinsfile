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
                echo 'Building Docker image...'
                script {
                    dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
                }
            }
        }
        stage('Run Container (Test)') {
            steps {
                echo 'Running temporary test container...'
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
                echo 'Pushing Docker image to DockerHub...'
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDS}") {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Code Quality - SonarQube') {
            steps {
                echo '🔎 Running SonarQube code quality analysis...'
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh '''
                    ${tool 'SonarScanner'}/bin/sonar-scanner \
                      -Dsonar.projectKey=testproject \
                      -Dsonar.projectName=Naa-Chitti-Lokam \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=http://localhost:9000 \
                      -Ds
                      onar.login=$SONAR_TOKEN
                    '''
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                echo '🚀 Deploying application to Kubernetes (Minikube)...'
                sh '''
                echo "➡ Checking Minikube status..."
                minikube status || minikube start

                echo "➡ Applying Kubernetes manifests..."
                kubectl apply -f naa-chitti-deployment.yaml --validate=false

                echo "➡ Verifying deployed resources..."
                kubectl get pods
                kubectl get svc
                kubectl get deployments
                '''
            }
        }
    }
    post {
        success {
            echo "✅ Phase Success: All phases are yet to complete"
        }
        failure {
            echo "❌ Phase Failure: Check pipeline logs for details."
        }
        always {
                script {
                    echo "🧹 Cleaning up test container..."
                    sh 'docker rm -f naa-chitti-test || true'
                }
        }
    }
}


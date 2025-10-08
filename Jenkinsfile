pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = "shinykuchi/naa-chitti-lokam-webapp"
        DOCKER_CREDS = "docker-credentials"
        SONARQUBE_ENV = "SonarQube"
        SONAR_TOKEN = credentials('sonarqube token')
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shinydevlearn-2022/Naa-Chitti-Lokam.git'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
                }
            }
        }

        stage('Run Container (Test)') {
            steps {
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
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDS}") {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Code Quality - SonarQube') {
            steps {
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
    }

    post {
        success {
            echo "✅ Phase Success: SonarQube analysis and Docker push completed successfully!"
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


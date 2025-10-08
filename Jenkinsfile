pipeline {
  agent any
  environment {
    DOCKER_HUB_REPO = "shinykuchi/naa-chitti-lokam-webapp"
    DOCKER_CREDS = "docker-credentials"
    SONARQUBE_ENV = "SonarQube"
  }
  stages {
    stage ('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/shinydevlearn-2022/Naa-Chitti-Lokam.git'
      }
    }

    stage ('Docker Build') {
      steps {
        script {
          dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
        }
      }
    }

    stage ('Run Container(Test)') {
      steps {
        script {
          sh 'docker run -d -p 3000:3000 --name naa-chitti-test ${DOCKER_HUB_REPO}:latest'
        }
      }
    }

    stage ('Check running Containers') {
      steps {
        sh 'docker ps -a | grep naa-chitti-test || true'
        sh 'docker port naa-chitti-test || true'
      }
    }

    stage ('Docker Push') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'docker-credentials') {
            dockerImage.push()
          }
        }
      }
    }
    stage ('Code Quality - SonarQube') {
      steps {
        withSonarQubeEnv('SonarQube') {
          sh '''
              sonar-scanner \
                -Dsonar.projectKey=testproject \
                -Dsonar.projectName=Naa-Chitti-Lokam \
                -Dsonar.sources=. \
                -Dsonar.host.url=http://localhost:9000 \
                -Dsonar.login=$sonar_token
          '''
        }
      }
    }
  }
  post {
    success {
      echo "Phase Success: SonarQube analysis is completed"
    }
    failure {
      echo "Phase Failure: Check logs."
    }
    always {
      // Clean up container if exists
      sh 'docker rm -f naa-chitti-test || true'
    }
  }
}

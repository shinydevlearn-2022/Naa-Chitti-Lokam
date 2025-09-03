pipeline {
  agent any
  environment {
    DOCKER_HUB_REPO = "shinykuchi/naa-chitti-lokam-webapp"
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
          dockerImage = docker.build("naa-chitti-lokam:latest")
        }
      }
    }
    stage ('Run Container(Test)') {
      steps {
        script {
          sh 'docker run -d -p 0:3000 --name naa-chitti-test naa-chitti-lokam:latest'
        }
      }
    }
    stage ('Check running Containers') {
      steps {
        sh 'docker ps -a || grep naa-chitti-test'
        sh 'docker port naa-chitti-test'
      }
    }
  }
  stage ('Docker Push') {
    steps {
      script {
        docker.withRegistry('http://index.docker.io/v1/', 'docker-credentials') {
        }
      }
    }
  }
  post {
    success {
      echo "Phase Success: Docker image build and pushed to DockerHub!"
    }
    failure {
      echo "Phase Failure: Check Docker build logs."
    }
    always {
      //Clean up container if exists
      sh 'docker rm -f naa-chitti-test || true'
    }
  }
}

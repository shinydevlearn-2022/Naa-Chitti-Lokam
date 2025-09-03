pipeline {
  agent any
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
          sh 'docker run -d -p 3000:3000 --name naa-chitti-test naa-chitti-lokam:latest'
        }
      }
    }
  }
  post {
    success {
      echo "Phase Success: Docker image build and container started!"
    }
    failure {
      echo "Phase Failure: Check Docker build logs."
    }
    always {
      //Clean up container if exists
      sh 'docker rm -f "naa-chitti-test || true'
    }
  }
}

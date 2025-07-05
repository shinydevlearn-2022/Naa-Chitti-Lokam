pipeline {
  agent any

  stages {
    stage('Clone Repository') {
      steps {
        git 'https://github.com/shinydevlearn-2022/Naa-Chitti-Lokam.git'
      }
    }

    stage('Build Website') {
      steps {
        echo 'Website files pulled successfully!'
      }
    }
  }

  post {
    success {
      echo 'Build completed successfully!'
    }
    failure {
      echo 'Build failed.'
    }
  }
}

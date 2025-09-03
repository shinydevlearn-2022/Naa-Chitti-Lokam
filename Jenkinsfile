pipeline {
  agent any
  stages {
    stage ('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/shinydevlearn-2022/Naa-Chitti-Lokam.git'
      }
    }
  } 
  post {
    success {
      echo "Phase 1 Success: Code successfully checked out from GitHub!"
    }
    failure {
      echo "Could not checkout code from GitHub."
    }
  }
}

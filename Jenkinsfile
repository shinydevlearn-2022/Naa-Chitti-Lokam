pipeline {
  agent any
  stages {
    stage ('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/shinydevlearn-2022/Naa-Chitti-Lokam.git'
      }
    }
    stage ('Archieve Website files') {
      steps {
        archieveArtifacts artifacts: '**/*.html, **/*.css, **/*.js, **/*.png, **/*.jpg', fingerprint: true
      }
    }
  } 
  post {
    success {
      echo "Phase 1 Success: Website files archieved in Jenkins"
    }
    failure {
      echo "Phase 1 Failed: Check logs"
    }
  }
}

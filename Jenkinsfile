pipeline {
  agent any
  tools {
    maven 'MavenLocal'
  }
  stages {
    stage ('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/shinydevlearn-2022/Naa-Chitti-Lokam.git'
      }
    }
    stage ('Build with Maven') {
      steps {
        sh 'mvn clean install - DskipTests'
      }
    }
  }
  post {
    success {
      echo "Phase 1 Success: Code checked out and Maven build completed"
    }
    failure {
      echo "Phase 1 Failed: Check Maven logs"
    }
  }
}

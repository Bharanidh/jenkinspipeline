pipeline {
  agent any
  stages {
    stage('Build and Deploy') {
      steps {
        echo 'building'
      }
    }
    stage('Deploy to Test?') {
      steps {
        timeout(time: 30, unit: 'DAYS') {
          input 'Deploy to Test?'
        }
        
      }
    }
    stage('Deploy') {
      steps {
        echo 'deploying'
      }
    }
  }
}
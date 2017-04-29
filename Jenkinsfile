pipeline {
  agent any
  stages {
    stage('Build & unit tests') {
      steps {
        echo 'running build and unit tests'
      }
    }
    stage('Automated Acceptance tests') {
      steps {
        echo 'running automated acceptance tests'
      }
    }
    stage('User acceptance tests') {
      steps {
        timeout(time: 30, unit: 'DAYS') {
          input 'Deploy to Stage?'
        }
        
        echo 'User acceptance tests ok, deploying to LIVE'
      }
    }
    stage('Release') {
      steps {
        echo 'deplying to live; running smoke tests'
      }
    }
  }
}
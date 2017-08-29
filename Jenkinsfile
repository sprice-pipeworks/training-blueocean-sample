pipeline {
  agent {
    docker {
      image 'bitwiseman/training-blueocean-sample'
      args '-u root -v $HOME/.m2:/root/.m2'
    }
    
  }
  stages {
    stage('Build') {
      steps {
        sh './jenkins/build.sh'
        archiveArtifacts 'target/*.war'
      }
    }
    stage('Test') {
      steps {
        parallel(
          "Backend": {
            sh './jenkins/test-all.sh'
            junit '**/surefire-reports/**/*.xml'
            sh './jenkins/test-backend.sh'
            
          },
          "Frontend": {
            sh './jenkins/test-frontend.sh'
            junit '**/test-results/karma/*.xml'
            
          },
          "Static": {
            sh './jenkins/test-static.sh'
            
          },
          "Performance": {
            sh './jenkins/test-performance.sh'
            
          }
        )
      }
    }
    stage('Dev') {
      steps {
        sh './jenkins/deploy.sh dev'
      }
    }
    stage('Staging') {
      steps {
        input(message: 'Deploy to staging?', ok: '\'Fire away!\'')
        sh './jenkins/deploy.sh staging'
        sh 'echo Notifying appropriate team members!'
      }
    }
  }
}
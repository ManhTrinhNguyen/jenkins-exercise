pipeline {
  agent any 
  tools {
    nodejs 'nodejs-20.10'
  }

  stages {
    stage('Increment Version') {
      steps {
        script {
          echo 'Read version'
          def packageJson = readJSON file: 'package.json'
          env.APP_VERSION = packageJson.version
          echo "$APP_VERSION"
        }
      }
    }
    stage('Testing stage'){
      steps {
        script {
          echo 'Testing stage'
        }
      }
    }
    stage('Building stage'){
      steps {
        script {
          echo 'Building stage'
        }
      }
    }
    stage('Deploy Stage'){
      steps {
        script {
          echo 'Deploy Stage'
        }
      }
    }
    stage ('Automatic Commit to Git repo'){
      steps {
        script {
          echo 'Automatic Commit'
        }
      }
    }
  }
}

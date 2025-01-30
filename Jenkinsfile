pipeline {
  agent any 
  tools {
    nodejs 'nodejs-20.8'
  }
  environment {
    DOCKER_REPO='nguyenmanhtrinh/demo-app'
  }

  stages {
    stage('Increment Version') {
      steps {
        script {
          echo 'update version'
          sh ''' 
            cd ./app
            npm install
            npx auto-version --patch
          '''

          echo 'Read version'
          def packageJson = readJSON file: './app/package.json'
          def app_version = packageJson.version
          env.IMAGE_NAME = "node-app-${app_version}"
          echo "${env.IMAGE_NAME}"
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

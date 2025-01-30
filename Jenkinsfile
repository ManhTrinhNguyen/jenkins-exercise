pipeline {
  agent any 
  tools {
    nodejs 'nodejs-20.10'
  }
  environments {
    DOCKER_REPO='nguyenmanhtrinh/demo-app'
  }

  stages {
    stage('Increment Version') {
      steps {
        script {
          echo 'Read version'
          def packageJson = readJSON file: './app/package.json'
          def app_version = packageJson.version
          env.IMAGE_NAME = "node-app-${app_version}"
          echo "${env.DOCKER_REPO}${env.IMAGENAME}"
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

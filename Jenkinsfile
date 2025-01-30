library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
  [
    $class: 'GitSCMSource',
    remote: 'https://github.com/ManhTrinhNguyen/Jenkins-Docker-Excercise-Shared-Library.git',
    credentialsId: 'github-credentials'
  ]
)

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
          echo 'update version...'
          updateNodejsVersion 'patch'

          echo 'Read version'

          def packageJson = readJSON file: './app/package.json'
          def app_version = packageJson.version
          env.IMAGE_NAME = "node-app-${app_version}"
        }
      }
    }
    stage('Testing stage'){
      steps {
        script {
          runNodejsTest
        }
      }
    }
    stage('Build and Push docker images .....'){
      steps {
        script {
          echo 'Building Docker Images ...'
          buildDockerImage "${env.DOCKER_REPO}:${env.IMAGE_NAME}"

          echo 'Login to docker hub'
          dockerLoginToDockerHub

          echo 'Push Docker Images'
          pushDockerImage "${env.DOCKER_REPO}:${env.IMAGE_NAME}"
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
          echo 'Commit and push to Git Repo .....'
          pushToGithub "github.com/ManhTrinhNguyen/jenkins-exercise.git" "Using-Shared-Library"
        }
      }
    }
  }
}

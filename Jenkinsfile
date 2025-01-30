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
          echo 'Testing ...'
          sh '''
            cd ./app
            npm run test
          '''
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
    stage('Print ENV'){
      environment {
        GIT_HUB_ACCESS = credentials('github-credentials')
      }
      steps {
        script {
          echo 'My Username is ...'
          sh "${GIT_HUB_ACCESS_USR}"

          echo 'my password is ...'
          sh "${GIT_HUB_ACCESS_PSW}"
        }
      }
    }
    stage ('Automatic Commit to Git repo'){
      steps {
        script {
          echo 'Commit and push to Git Repo .....'
          withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'USER', passwordVariable: 'PWD')]){
            sh "git remote set-url origin https://${USER}:${PWD}@github.com/ManhTrinhNguyen/jenkins-exercise.git"

            sh 'git config --global user.email jenkins@gmail.com'
            sh 'git config --global user.name Jenkins'

            sh 'git status'
            sh 'git config --list'

            sh 'git add .'
            sh 'git commit -m "ci: bump version"'
            sh 'git push origin HEAD:Using-Shared-Library'
          }
          
        }
      }
    }
  }
}

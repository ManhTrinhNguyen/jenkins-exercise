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
          echo 'Building Docker IMAGES'
          withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USER', passwordVariable: 'PWD')]){
            sh "docker build -t ${env.DOCKER_REPO}:${env.IMAGE_NAME} ."
            sh "echo ${PWD} | docker login -u ${USER} --password-stdin"
            sh "docker push ${env.DOCKER_REPO}:${env.IMAGE_NAME}"
          }
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
          sh 'git remote '
          sh 'git add .'
          sh 'git commit -m "ci: bump version"'
          sh 'git push origin HEAD:main'
        }
      }
    }
  }
}

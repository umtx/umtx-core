pipeline {

  agent any

  environment {
    DOCKERHUB_CREDENTIALS = credentials('DOCKERHUB')
    GITHUB_REPO = 'git@github.com:umtx/umtx-bot.git'
    DOCKER_REPO = 'binhotvn/umt-x-bot-sis'
  }

  stages {
    stage('Checkout external proj') {
      steps {
        checkout scmGit(branches: [
          [name: '*/main']
        ], extensions: [submodule(parentCredentials: true, recursiveSubmodules: true, reference: '')], userRemoteConfigs: [
          [credentialsId: 'GITHUB_BOT_SSH', url: GITHUB_REPO]
        ])
      }
    }
    stage('Build') {

      steps {
        sh 'docker build -t $DOCKER_REPO .'

      }
    }

    stage('Login') {

      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }

    stage('Push') {

      steps {
        sh 'docker push $DOCKER_REPO'
        sh 'docker tag $DOCKER_REPO $DOCKER_REPO:${BUILD_NUMBER}'
        sh 'docker push $DOCKER_REPO:${BUILD_NUMBER}'
      }
    }
    stage('Clean') {
      steps {
        sh 'docker rmi $DOCKER_REPO:${BUILD_NUMBER}'
        sh 'docker rmi $DOCKER_REPO:latest'
      }

    }

  }

}

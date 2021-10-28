pipeline {
  agent any

  tools {
    jdk 'jdk'
    maven 'maven'
  }

  stages {
    stage('Build') {
      steps {
        withMaven(maven : 'maven') {
          sh "mvn clean install"
        }
      }
    }

    stage('Create and push container') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
          withMaven(maven : 'maven') {
            sh "mvn jib:build"
          }
        }
      }
    }
  }
}

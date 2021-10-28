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
          sh "mvn package"
        }
      }
    }

    stage ('OWASP Dependency-Check Vulnerabilities') {
      steps {
        withMaven(maven : 'maven') {
          sh 'mvn dependency-check:check'
        }

        dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
      }
    }

    stage('SonarQube analysis') {
      steps {
        withSonarQubeEnv(credentialsId: 'sonarqube-secret', installationName: 'sonarqube-server') {
          withMaven(maven : 'maven') {
            sh 'mvn sonar:sonar -Dsonar.dependencyCheck.jsonReportPath=target/dependency-check-report.json -Dsonar.dependencyCheck.xmlReportPath=target/dependency-check-report.xml -Dsonar.dependencyCheck.htmlReportPath=target/dependency-check-report.html'
          }
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

    stage('Anchore analyse') {
      steps {
        writeFile file: 'anchore_images', text: 'docker.io/maartensmeets/spring-boot-demo'
        anchore name: 'anchore_images'
      }
    }
  }
}

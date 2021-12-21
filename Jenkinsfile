pipeline {
    agent any
    tools {
        jdk 'jdk-11'
        maven 'maven'
    }
    stages {
        stage("Junit Test") {
            steps {
                sh "mvn clean test"
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage("SonarQube Analysis") {
            steps {
                withSonarQubeEnv(credentialsId: "SonarQubeToken", installationName: "SonarQubeServer") {
                    sh "mvn clean package sonar:sonar"
                }
            }
        }
        stage("Build Package") {
            steps {
                sh "mvn -B -DskipTests clean package"
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

pipeline {
  agent none
  stages {
    stage('Checkout') {
      agent {
        docker { image 'maven:3-eclipse-temurin-21' }
      }
      steps {
        git branch: 'main', url: 'git@github.com:kevin-infra/source-maven-java-spring-hello-webapp.git'
      }
    }
    stage('Test Application') {
      agent {
        docker { image 'maven:3-eclipse-temurin-21' }
      }
      steps {
        sh 'mvn test'
      }
    }
    stage('Build Application') {
      agent {
        docker { image 'maven:3-eclipse-temurin-21' }
      }
      steps {
        sh 'mvn clean package -DskipTests=true'
      }
    }
    stage('Build Container Image') {
      agent { label 'controller' }
      steps {
        sh 'docker build -t hello-webapp .'
      }
    }
    stage('Tag Container Image') {
      agent { label 'controller' }
      steps {
        sh 'docker tag hello-webapp kevin-infra/hello-webapp:${BUILD_NUMBER}' // Tagging with build number
        sh 'docker tag hello-webapp kevin-infra/hello-webapp:latest' // Tagging with latest
      }
    }
    stage('Push Container Image') {
      agent { label 'controller' }
      steps {
        withDockerRegistry(credentialsId: 'docker-registry-credential', url: 'https://index.docker.io/v1/') {
          sh 'docker push kevin-infra/hello-webapp:${BUILD_NUMBER}' // Tagging with build number
          sh 'docker push kevin-infra/hello-webapp:latest' // Tagging with latest
        }
      }
    }
    stage('Run Container') {
      agent { label 'controller' }
      steps {
        sh 'docker container run --detach --name hello-webapp-con -p 80:8080 kevin-infra/hello-webapp:${BUILD_NUMBER}'
      }
    }
  }
}

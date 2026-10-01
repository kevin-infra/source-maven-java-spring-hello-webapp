pipeline {
  agent none
  stages {
    stage('Checkout') {
      agent {
        docker { image 'maven:3-eclipse-temurin-21' }
      }
      steps {
        git branch: 'main', url: 'https://github.com/kevin-infra/source-maven-java-spring-hello-webapp.git'
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
        sh 'docker image build -t my-tomcat .'
      }
    }
    stage('Tag Container Image') {
      agent { label 'controller' }
      steps {
        sh 'docker tag my-tomcat sesac91/my-tomcat:${BUILD_NUMBER}' // Tagging with build number
        sh 'docker tag my-tomcat sesac91/my-tomcat:latest' // Tagging with latest
      }
    }
    stage('Push Container Image') {
      agent { label 'controller' }
      steps {
        withDockerRegistry(credentialsId: 'docker-registry-credential', url: 'https://index.docker.io/v1/') {
          sh 'docker image push sesac91/my-tomcat:${BUILD_NUMBER}' // Tagging with build number
          sh 'docker image push sesac91/my-tomcat:latest' // Tagging with latest
        }
      }
    }
    stage('Run Container') {
      agent { label 'controller' }
      steps {
        sh 'docker container run --detach --name myweb -p 80:8080 sesac91/my-tomcat:latest'
      }
    }
  }
}

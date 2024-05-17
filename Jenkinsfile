pipeline {
  agent any

  tools {
    maven 'my-maven'
  }

  stages {

    stage('Build with Maven') {
      steps {
        sh 'mvn --version'
        sh 'java -version'
        sh 'mvn clean package'
      }
    }

    stage('Packaging/Pushing imagae') {
      steps {
        withDockerRegistry(credentialsId: 'dockerhub', url: 'https://index.docker.io/v1/') {
          sh 'docker build -t congy17/demo .'
          sh 'docker push congy17/demo'
        }
      }
    }

    stage('Deploy Spring Boot to DEV') {
      steps {
        echo 'Deploying and cleaning'
        sh 'docker image pull congy17/demo'
        sh 'docker container stop docker-springboot || echo "this container does not exist" '
        sh 'docker network create dev || echo "this network exists"'
        sh 'echo y | docker container prune '

        sh 'docker container run -d --rm --name docker-springboot -p 8090:8080 --network dev congy17/demo'
      }
    }
  }

  post {
    // Clean after build
    always {
      cleanWs()
    }
  }
}

pipeline {
    agent {
        node {
            label 'dockerhost-build-server'
        }
    }

    tools {
        // Esto le dice a Jenkins que use el Maven que configuraste en su web
        maven 'maven-3.9.6'
    }

    environment {
        // Esto fuerza a Maven a usar el Java 17 de la VM de Docker
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
        PATH = "${JAVA_HOME}/bin:${PATH}"
    }

    stages {
        stage('Packaging') {
            steps {
                echo 'Packaging application...'
                // Ahora Maven usará obligatoriamente Java 17
                sh 'mvn clean package'
            }
        }

        stage('Copying jar file') {
            steps {
                echo 'Copying jar file...'
                sh 'mv target/*.jar .'
            }
        }

        stage('cleanup') {
            steps {
                echo 'Cleaning up old backend containers...'
                sh 'docker system prune -a --volumes --force --filter "label=campaign-demo-server"'
            }
        }

        stage('build image') {
            steps {
                echo 'Building backend image...'
                sh 'docker build -t darkisma/campaign-demo:v1 --label campaign-demo-server .'
            }
        }

        stage('run container') {
            steps {
                echo 'Starting backend container...'
                sh 'docker run -d --name campaign-demo-server --label campaign-demo-server -p 5000:5000 darkisma/campaign-demo:v1'
            }
        }
    }
}

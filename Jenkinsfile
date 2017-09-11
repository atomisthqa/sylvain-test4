
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                }
        }
        stage('Build') {
            steps {
                sleep 20
                echo "built"
            }
        }
    }
}
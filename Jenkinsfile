pipeline {
    agent any

    tools {
        maven "maven"
    }

    environment {    
        DOCKERHUB_CREDENTIALS = credentials('dockerloginid')
    }

    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Perform SCM Checkout'
                git 'https://github.com/Nelztacy/bank-app'
            }
        }

        stage('Application_Build') {
            steps {
                echo 'Perform Maven Build'
                sh 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t nelzone/bankapp-eta-app:V${BUILD_NUMBER} ."
                sh 'docker image list'
                sh "docker tag nelzone/bankapp-eta-app:V${BUILD_NUMBER} nelzone/bankapp-eta-app:latest"
            }
        }

        stage('Approve - push Image to dockerhub') {
            steps {
                // Send an approval prompt
                script {
                    env.APPROVED_DEPLOY = input message: 'User input required. Choose "Yes" | "Abort"'
                }
            }
        }

        stage('Login2DockerHub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Publish_to_Docker_Registry') {
            steps {
                sh "docker push nelzone/bankapp-eta-app:latest"
            }
        }
    }
}

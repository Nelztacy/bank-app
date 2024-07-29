pipeline {

    agent any
	
    tools {
        maven "maven3"
    }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
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
              
        stage('Approve - push Image to dockerhub'){
            steps{
                
                //----------------send an approval prompt-------------
                script {
                   env.APPROVED_DEPLOY = input message: 'User input required Choose "Yes" | "Abort"'
                       }
                //-----------------end approval prompt------------
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
		stage('Deploy to Kubernetes_Cluster') {
			steps {
				script {
					sshPublisher(publishers: [sshPublisherDesc(configName: 'Kubernetes', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f k8sdeployment.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])	
				}
			}
		}
    }
}									

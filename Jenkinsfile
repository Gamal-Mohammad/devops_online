pipeline {

    agent any
/*
	tools {
        maven "maven"
    }
*/
    environment {
        registry = "gamalm2041/myapp_image"
        registryCredential = 'dockerhub'
    }

    stages{
        stage('BUILD'){
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }

        stage('Building image') {
            steps{
              script {
                dockerImage = docker.build registry + ":$BUILD_NUMBER"
              }
            }
        }
        
        stage('CODE ANALYSIS with SONARQUBE') {

            environment {
                scannerHome = tool 'mysonarscanner5'
            }
            steps {
                withSonarQubeEnv('sonar-pro') {
			sh '''mvn sonar:sonar \
  			-Dsonar.projectKey=mysonarscanner5 \
  			-Dsonar.host.url=192.168.2.67:9000 \
  			-Dsonar.login=fe66da68689a547fdd7fdfe9f7b9e12f8d3b43e7'''
                }

                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}

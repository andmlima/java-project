pipeline {
    agent any
    stages{
        stage('Fetch Code') {
            steps {
                git branch: 'tasks-backend', url: 'https://github.com/andmlima/java-project'
            }
        }
        stage('BUILD'){
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }
        stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }
        stage('Sonar Analysis'){
            environment {
                scannerHome = tool 'sonarscannerserver'
            }
            steps {
                withSonarQubeEnv(installationName: 'sonarqubeserver'){
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=java-projeto -Dsonar.host.url=${env.SONAR_HOST_URL} -Dsonar.login=aa91f973b6118e6da23b7777b3d7e309c490dafe  -Dsonar.java.binaries=target"
                }
            }
        }
        stage ('Quality Gate'){
            steps {
                sleep(5)
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('FrontEnd'){
            steps {
                dir('frontend'){
                    git branch: 'tasks-frontend', url: 'https://github.com/andmlima/java-project'
                    sh 'mvn clean package'
                }
            }
        }
        stage ('Deploy Prod'){
            steps {
				sh 'docker-compose build'
				sh 'docker-compose up -d'
            }
        }		
	}
}

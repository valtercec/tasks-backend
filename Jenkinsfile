pipeline {
	agent any
	stages {

		stage ('Build Backend') {
			steps {
				bat 'mvn clean package -DskipTests=true'
			}
		}

		stage ('Unit Tests') {
			steps {
				bat 'mvn test'
			}
		}

		stage ('Sonar Analysis') {
			environment {
				scannerHome = tool 'SONAR_SCANER'
			}
			steps {
				withSonarQubeEnv('SONAR_LOCAL') {
					bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=9830fb59d6ca0209c9dd00aadbf5cb79b10e3643 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
				}
			}
		}
		
		stage ('Quality Gate') {
			steps {
				sleep(5)
				timeout(time: 1, unit: 'MINUTES'){
					waitForQualityGate abortPipeline: true
				}
			}
		}
	}
}
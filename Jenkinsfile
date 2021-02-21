pipeline {
	agent any
	stages {

		stage ('Build Backend') {
			steps {
				sh 'mvn clean package -DskipTests=true'
			}
		}

		stage ('Unit Tests') {
			steps {
				sh 'mvn test'
			}
		}
		
		stage ('Deploy Backend') {
			steps {
				deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
			}
		}
		
		stage ('API Test') {
			steps {
				dir('api-test'){
					git credentialsId: 'github_login', url: 'https://github.com/valtercec/tasks-api-test'
					sh 'mvn test'
				}
			}
		}
		
		stage ('Deploy Frontend') {
			steps {
				dir('frontend'){
					git credentialsId: 'github_login', url: 'https://github.com/valtercec/tasks-frontend'
					sh 'mvn clean package'
					deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001')], contextPath: 'tasks', war: 'target/tasks.war'
				}
			}
		}
		
		stage ('Functional Test') {
			steps {
				dir('functional-test'){
					git credentialsId: 'github_login', url: 'https://github.com/valtercec/tasks-functional-tests'
					sh 'mvn test'
				}
			}
		}
		
		stage ('Deploy Prod') {
			steps {
				sh 'docker-compose build'
				sh 'docker-compose up -d'
			}
		}
		
		stage ('Health Ceck') {
			steps {
				sleep(15)
				dir('functional-test'){
					sh 'mvn verify -Dskip.surefire.tests'
				}
			}
		}
	}
	
	post {
	    always {
	        junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml, api-test/target/surefire-reports/*.xml, functional-test/target/surefire-reports/*.xml, functional-test/target/failsafe-reports/*.xml'
	    }

	}

}

// Declarative pipeline
pipeline {
    agent any
    stages {
        stage ('Clean Reports') {
            steps {
                sh 'rm -rf test-reports || true'  
            }
        }
	stage ('test') {
	    steps {
		sh 'coverage run test.py && coverage html && coverage report'
	    }
	}	
	stage('SonarQube Analysis') {
	    environment {
		scannerHome = tool 'SonarQube Scanner'
            }
	    steps {				
		withSonarQubeEnv('admin') {
		     sh '${scannerHome}/bin/sonar-scanner \
 	   	     -D sonar.projectKey=sampledevopsproject \
		     -D sonar.python.coverage.reportPaths=coverage.xml \
		     -D sonar.python.xunit.reportPath=test-reports/*.xml'	
		}
	    }
	}
        stage ('Generate Test Reports') {
            steps {
                junit 'test-reports/*.xml'
            }
        }
	stage ('Publish Artifactory') {
	    steps {
		    withCredentials([usernamePassword(credentialsId: 'artifactory', passwordVariable: 'passwd', usernameVariable: 'user')]) {
    		         sh 'jf rt upload test-reports/ python-app/'
		    }	
	    }
	}
    }
}

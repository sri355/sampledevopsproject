// Declarative pipeline
pipeline {
    agent any
    stages {
        stage ('Clean Reports') {
            steps {
		echo '********* Cleaning Workspace Stage Started **********'
                sh 'rm -rf test-reports || true'  
		echo '********* Cleaning Workspace Stage Finished **********'
            }
        }
	stage ('test') {
	    steps {
		echo '********* Test Stage Started **********'
		sh 'coverage run test.py && coverage html && coverage report -m'
		echo '********* Test Stage Finished **********'
	    }
	}
		
	stage('SonarQube Analysis') {
	    environment {
		scannerHome = tool 'SonarQube Scanner'
            }
	    steps {
		echo '********* Code Analysis Started **********'				
		withSonarQubeEnv('admin') {
		     sh '${scannerHome}/bin/sonar-scanner \
 	   	     -D sonar.projectKey=sampledevopsproject \
		     -D sonar.python.coverage.reportPaths=htmlcov'
		}
		echo '********* Code Analysis Finished **********'
	    }
	}
        stage ('Generate Test Reports') {
            steps {
		echo '********* Generate Test Reports Start **********' 
                junit 'test-reports/*.xml'
		echo '********* Generate Test Reports Finished **********' 
            }
        }
	stage ('Publish Artifactory') {
	    steps {
		echo '********* Publish Report JFrog Artifacts started **********' 
		    withCredentials([usernamePassword(credentialsId: 'artifactory', passwordVariable: 'passwd', usernameVariable: 'user')]) {
    		         sh 'jf rt upload test-reports/ python-app/'
		    }
		echo '********* Publish Report JFrog Artifacts Finished **********'	
	    }
	}
    }
}

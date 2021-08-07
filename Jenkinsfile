pipeline {
    agent any

    tools {
	maven 'Maven 3.8.1'
    }
    
    stages {
        stage('build') {
            steps {
                echo 'Compiling worker app'
		dir('worker') {
	           sh 'mvn compile'
                }
            }
        }
         stage('test') {
            steps {
                echo 'Running unit tests on worker app'
                sleep 9
            }
        }
         stage('package') {
            steps {
                echo 'Packaging worker app'
                sleep 5
            }
        }
    }
    
    post {
        always {
            echo 'Build pipeline for worker is complete..'
        }
    }
}

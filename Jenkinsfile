pipeline {

    agent none
    
    stages {
        stage('worker build') {
          agent {
            docker { 
             image 'maven:3.6.1-jdk-8-alpine' 
             args '-v $HOME/.m2:/root/.m2'
           }
          }
            when {
              changeset '**/worker/**'
            }
            steps {
              echo 'Compiling worker app'
	            sh 'cd worker && mvn compile'
            }
        }

         stage('worker test') {
           agent {
            docker { 
             image 'maven:3.6.1-jdk-8-alpine' 
             args '-v $HOME/.m2:/root/.m2'
           }
           }
            when {
              changeset '**/worker/**'
            }
            steps {
                echo 'Running unit tests on worker app'
                  sh 'cd worker && mvn clean test'
            }
        }
         
         stage('worker package') {
          agent {
            docker { 
             image 'maven:3.6.1-jdk-8-alpine' 
             args '-v $HOME/.m2:/root/.m2'
           }
          }
            when {
              branch 'master'
              changeset '**/worker/**'
            }
            steps {
                echo 'Packaging worker app'
                sh 'cd worker && mvn package -DskipTests'
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }

         stage('worker docker-package') {
            agent any
            when {
              changeset '**/worker/**'
            }
            steps {
              echo 'Packaging worker app with docker'
	            script {
                docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                    def workerImage = docker.build("josenoriega/worker:v${env.BUILD_ID}", "worker")
                    workerImage.push()
                    workerImage.push("${env.BRANCH_NAME}")
                }
              } 
            }
        }


        stage('result build') {
             agent {
              docker {
                image 'node:8.16.0-alpine'
              }
                }
            when {
              changeset '**/result/**'
            }
            steps {
                echo 'Compiling result app'
		        sh 'cd result && npm install'  
            }
        }
         stage('result test') {
              agent {
                docker {
                  image 'node:8.16.0-alpine'
                }
                  }
            when {
              changeset '**/result/**'
            }
            steps {
                echo 'Running unit tests on result app'
                  sh 'cd result && npm install'
                  sh 'cd result && npm test'
            }
        }

         stage('result docker-package') {
            agent any
            when {
              changeset '**/result/**'
            }
            steps {
              echo 'Packaging result app with docker'
	            script {
                docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                    def workerImage = docker.build("josenoriega/result:v${env.BUILD_ID}", "./result")
                    workerImage.push()
                    workerImage.push("${env.BRANCH_NAME}")
                }
              } 
            }
        }

        stage('vote build') {
            agent {
            docker {
               image 'python:2.7.16-slim'
                   args '--user root'
            }
          }

            steps {
                echo 'Compiling vote app'
                sh 'cd vote && pip install -r requirements.txt'

            }
        }
         stage('vote test') {
             agent {
                docker {
                   image 'python:2.7.16-slim'
                       args '--user root'
                }
            }

            steps {
                echo 'Running unit tests on vote app'
                 sh 'cd vote && pip install -r requirements.txt'
                 sh 'cd vote && nosetests -v'
            }
        }

         stage('vote docker-package') {
            agent any

            steps {
              echo 'Packaging vote app with docker'
                script {
                docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                    def workerImage = docker.build("josenoriega/vote:v${env.BUILD_ID}", "./vote")
                    workerImage.push()
                    workerImage.push("${env.BRANCH_NAME}")
                }
              } 
            }
        }

    }
}

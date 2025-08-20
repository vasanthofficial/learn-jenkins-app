pipeline {
    agent any
    stages {
        stage('Hello') {
            steps {
              echo 'Hello World'
            }
        }
        stage('Without Docker'){
            steps{
            echo 'Without docker image'
            sh 'touch "file_no.txt"'   
            }
        }
        stage('With docker image'){
             agent{
                    docker{
                        image 'node:current-alpine3.22'
                        reuseNode true /// Ensures this uses the same workspaces
                    }
                }
            steps{
                echo 'With docker image'
                  sh 'touch "file_yes.txt"'
                  sh 'npm --version'
            }
        }
    }
}
  
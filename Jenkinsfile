pipeline{
    agent any
    environment{
        REACT_APP_VERSION='1.5.$BUILD_ID'  
    }
    stages{
       stage('Build'){
        agent{
            docker{
                  image 'node:18-alpine'
                  reuseNode true
                  }
            }
            steps{
                   sh '''
                    echo "Build process started..."
                    npm --version
                    npm ci
                    npm run build
                '''
            }
       }
       stage('Building image'){
        agent{
            docker{
                image 'custom-aws'
                reuseNode true
                args '-u root -v /var/run/docker.sock:/var/run/docker.sock --entrypoint=""'
            }
        }
        steps{
            // aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 211125779092.dkr.ecr.us-east-1.amazonaws.com
            // docker tag jenkins-app:latest 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest
            // docker push 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest
            sh '''
            docker build -t jenkins-app .
            '''        
        }
       }
       stage('Deploy AWS'){
          agent{
            docker{
                image 'custom-aws'
                reuseNode true
                args '-u root -v /var/run/docker.sock:/var/run/docker.sock --entrypoint=""'

                }
          }
        steps{
                            //  aws ecs register-task-definition --cli-input-json file://learn-jenkins-app/aws/task-definition.json

            //   docker info
            //   curl -v https://211125779092.dkr.ecr.us-east-1.amazonaws.com/v2/
            //   aws ecr get-login-password --region us-east-1 | head -c 20


            //   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 211125779092.dkr.ecr.us-east-1.amazonaws.com
            //   docker tag jenkins-app:latest 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest
            //   docker push 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest
            withCredentials([usernamePassword(credentialsId: 'aws_key', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
              sh '''
            docker info
            curl -v https://211125779092.dkr.ecr.us-east-1.amazonaws.com/v2/
            aws ecr get-login-password --region us-east-1 | head -c 20


            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 211125779092.dkr.ecr.us-east-1.amazonaws.com
            docker tag jenkins-app:latest 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest
            docker push 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest
             '''
            }
        }
       }
    }
}
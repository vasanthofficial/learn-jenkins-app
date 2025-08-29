pipeline{
    agent any
    environment{
        REACT_APP_VERSION="1.5.$BUILD_ID"
        AWS_DOCKER_REGISTRY="211125779092.dkr.ecr.us-east-1.amazonaws.com"  
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
            sh '''
            echo $REACT_APP_VERSION
            docker build -t $AWS_DOCKER_REGISTRY/jenkins-container:$REACT_APP_VERSION .
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
            withCredentials([usernamePassword(credentialsId: 'aws_key', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
              sh '''
            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 211125779092.dkr.ecr.us-east-1.amazonaws.com
            docker tag $AWS_DOCKER_REGISTRY/jenkins-container:$REACT_APP_VERSION 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest
            docker push 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest

           TASK_VERSION=$(aws ecs register-task-definition --cli-input-json file://aws/task-definition.json | jq -r '.taskDefinition.revision')
            aws ecs update-service \
            --cluster Jenkins-cluster \
            --service Jenkins-server-nginx-service \
            --task-definition Jenkins-server-nginx-service:$TASK_VERSION
             '''
            }
        }
       }
    }
}
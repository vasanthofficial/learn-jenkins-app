pipeline{
    agent any
    stages{
       stage('BUILD'){
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
       stage('Building docker image'){
        agent{
            docker{
                image 'amazonlinux:2'
                reuseNode true
                args '-u root -v /var/run/docker.sock:/var/run/docker.sock --entrypoint=""'
            }
        }
        steps{
         withCredentials([usernamePassword(credentialsId: 'aws_key', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
            sh '''
            amazon-linux-extras install docker
            docker build -t jenkins-app .
            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 211125779092.dkr.ecr.us-east-1.amazonaws.com
            docker tag jenkins-app:latest 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest
            docker push 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest
            '''        
         }
        }
       }
    }
}
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
       stage('DOCKER IMAGE'){
        steps{
            sh 'docker build -t jenkins-app .'
        }
       }
       stage('ECR PUSH'){
        steps{
            sh '''
            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 211125779092.dkr.ecr.us-east-1.amazonaws.com
            docker tag jenkins-app:latest 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest
            docker push 211125779092.dkr.ecr.us-east-1.amazonaws.com/jenkins-app:latest
            '''
        }
       }
    }
}
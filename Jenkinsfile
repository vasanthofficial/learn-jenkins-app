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
            sh 'docker build -t my-custom-img .'
        }
       }
    }
}
/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any
    stages {
        stage('Build') {
                agent {
                docker {
                    image 'node:18-alpine'
                }
                }
            steps {
                sh '''
                echo "Build process started..."
                npm --version
                npm ci
                npm run build
               '''
            }
        }
        stage('Test') {
            steps {
                sh '''
             if [ -f /build/index.html ]; then
             echo "File exists"
             else
             echo "File does not exist"
             npm test
             fi
             '''
            }
        }
    }
}

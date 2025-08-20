pipeline{
        stage('Build'){
                agent {
        docker{
            image 'node:18-alpine'
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
    }

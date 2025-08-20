pipeline{
    agent {
        docker{
            image 'node-18:alpine'
        }
        stages('Build'){
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
}
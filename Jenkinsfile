/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any
    stages {
        // stage('Build') {
        //         agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //         }
        //     steps {
        //         sh '''
        //         echo "Build process started..."
        //         npm --version
        //         npm ci
        //         npm run build
        //        '''
        //     }
        // }
        stage('Test') {
                agent {
                docker {
                    /* groovylint-disable-next-line DuplicateStringLiteral */
                    image 'node:18-alpine'
                    reuseNode true
                }
                }
            steps {
                sh '''
             if [ -f build/index.html ]; then
             echo "File exists"
             else
             echo "File does not exist"
             fi
             echo "Starting the test process"
             npm test
             '''
            }
        }
        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }

            steps {
                sh '''
            npm install -g serve
            serve -s build &
            sleep 30
            npx playwright test
        '''
            }
        }
    }
// post{
//     always{
//         junit 'jest-results/junit.xml'
//     }
// }
}

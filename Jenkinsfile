/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any
    stages {
        stage('Build') {
                agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
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
            npm install serve
            node_modules/.bin/serve -s build &
            sleep 20
            npx playwright test
            npx playwright test --reporter=line
        '''
            }
        }
    }
post{
    always{
        junit 'jest-results/junit.xml'
            /* groovylint-disable-next-line LineLength */
publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Reports', reportTitles: '', useWrapperFileDirectly: true])    }
}
}

    /* groovylint-disable-next-line CompileStatic */
pipeline {
        agent any
        environment {
            NETLIFY_SITE_ID = '62a7e663-dcfb-4769-9525-5b24df10cac1'
            NEFTLIFY_AUTH_TOKEN = credentials('secretkey')
        }
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
            stage('Deploy') {
                agent {
                    docker {
                        image 'node:18-alpine'
                        reuseNode true
                    }
                }
                steps {
                    sh '''
                     echo "Deployment started"
                     npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify deploy --dir=build --prod
                    echo "This project Id of the website is: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    '''
                }
            }
        }
    post {
        always {
            junit 'jest-results/junit.xml'
            /* groovylint-disable-next-line LineLength */
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Reports', reportTitles: '', useWrapperFileDirectly: true])    }
    }
}

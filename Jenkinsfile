pipeline {
        agent any
        environment {
            NETLIFY_SITE_ID = '62a7e663-dcfb-4769-9525-5b24df10cac1'
            NETLIFY_AUTH_TOKEN = credentials('secretkey')
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
                post {
                    always {
                            junit 'jest-results/junit.xml'
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Local Reports', reportTitles: '', useWrapperFileDirectly: true])   
                        }
                     }
            }
            stage('UAT Deployment') {
                agent {
                    docker {
                        image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                        reuseNode true
                    }
                }
                environment{
                    CI_ENVIRONMENT_URL = "Place-holder"
                }
                steps {
                sh '''  echo "Deployment started"
                        npm install netlify-cli@20.1.1 node-jq
                        node_modules/.bin/netlify --version
                        echo "This project Id of the website is: $NETLIFY_SITE_ID"
                        node_modules/.bin/netlify status
                        node_modules/.bin/netlify deploy --dir=build  --json >  deployment.json 
                        CI_ENVIRONMENT_URL=$(node_modules/.bin/node-jq -r '.deploy_url' deployment.json)
                        npx playwright test --reporter=line
                    '''
                }
                post {
                    always {
                            junit 'jest-results/junit.xml'
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Staging Reports', reportTitles: '', useWrapperFileDirectly: true])   
                        }
                   }
            }
            stage('Approval'){
                steps{
                      timeout(time: 120, unit: 'SECONDS') {
                      input 'Approval for deployment'
                      }
                }
            }
            stage('Production deployment') {
                agent {
                    docker {
                        image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                        reuseNode true
                    }
                }
                environment{
                    CI_ENVIRONMENT_URL = 'https://fabulous-hummingbird-413a57.netlify.app'
                }
                steps {
                sh '''   node_modules/.bin/netlify --version
                         echo "This project Id of the website is: $NETLIFY_SITE_ID"
                         node_modules/.bin/netlify status
                         node_modules/.bin/netlify deploy --dir=build --prod
                         npx playwright test --reporter=line
                    '''
                }
                post {
                    always {
                            junit 'jest-results/junit.xml'
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Prod Reports', reportTitles: '', useWrapperFileDirectly: true])   
                        }
                   }
            }
        }

}

 
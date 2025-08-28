pipeline {
        agent any
        environment {
            NETLIFY_SITE_ID = '62a7e663-dcfb-4769-9525-5b24df10cac1'
            NETLIFY_AUTH_TOKEN = credentials('secretkey')
            // REACT_APP_VERSION = "2.5.$BUILD_ID"
        }
        stages {
          
         
            stage('AWS'){
             agent {
                docker{
                    image 'amazon/aws-cli'
                    reuseNode true
                    args '-u root --entrypoint=""'
                }
               }
              steps{
                withCredentials([usernamePassword(credentialsId: 'aws-key', passwordVariable:'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                sh '''
                // aws --version
                // aws s3api create-bucket \
                // --bucket jenkins-webby-bucket01 \
                // --region us-east-1
                // aws s3 sync build s3://jenkins-webby-bucket01
                aws ecs register-task-definition  --cli-input-json file://src\aws\task-definition.json
                '''
                }
              }
            }
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
            // stage('Test') {
            //         agent {
            //         docker {
            //             image 'node:18-alpine'
            //             reuseNode true
            //         }
            //         }
            //     steps {
            //         sh '''
            //     if [ -f build/index.html ]; then
            //     echo "File exists"
            //     else
            //     echo "File does not exist"
            //     fi
            //     echo "Starting the test process"
            //     npm test
            //     '''
            //     }
            // }
            // stage('E2E') {
            //     agent {
            //         docker {
            //             image 'custom-build'
            //             reuseNode true
            //         }
            //     }
            //     steps {
            //     sh '''
            //             serve -s build &
            //             sleep 20
            //             npx playwright test
            //             npx playwright test --reporter=line
            //         '''
            //     }
            //     post {
            //         always {
            //                 junit 'jest-results/junit.xml'
            //                 publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Local Reports', reportTitles: '', useWrapperFileDirectly: true])   
            //             }
            //          }
            // }
            // stage('UAT Deployment') {
            //     agent {
            //         docker {
            //             image 'custom-build'
            //             reuseNode true
            //         }
            //     }
            //     environment{
            //         CI_ENVIRONMENT_URL = "Place-holder"
            //     }
            //     steps {
            //     sh '''  echo "Deployment started"
            //             netlify --version
            //             echo "This project Id of the website is: $NETLIFY_SITE_ID"
            //             netlify status
            //             netlify deploy --dir=build  --json >  deployment.json 
            //             CI_ENVIRONMENT_URL=$(node-jq -r '.deploy_url' deployment.json)
            //             npx playwright test --reporter=line
            //         '''
            //     }
            //     post {
            //         always {
            //                 junit 'jest-results/junit.xml'
            //                 publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Staging Reports', reportTitles: '', useWrapperFileDirectly: true])   
            //             }
            //        }
            // }
            // stage('Approval'){
            //     steps{
            //           timeout(time: 120, unit: 'SECONDS') {
            //           input 'Approval for deployment'
            //           }
            //     }
            // }
            // stage('Production deployment') {
            //     agent {
            //         docker {
            //             image 'custom-build'
            //             reuseNode true
            //         }
            //     }
            //     environment{
            //         CI_ENVIRONMENT_URL = 'https://fabulous-hummingbird-413a57.netlify.app'
            //     }
            //     steps {
            //     sh '''   netlify --version
            //              echo "This project Id of the website is: $NETLIFY_SITE_ID"
            //              netlify status
            //              netlify deploy --dir=build --prod
            //              npx playwright test --reporter=line
            //         '''
            //     }
            //     post {
            //         always {
            //                 junit 'jest-results/junit.xml'
            //                 publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Prod Reports', reportTitles: '', useWrapperFileDirectly: true])   
            //             }
            //        }
            // }
        }

}

 
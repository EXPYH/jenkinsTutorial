pipeline {
    agent any

    triggers{
        pollSCM('*/3 * * * *')
    }

    environment{
        AWS_ACCESS_KEY_ID = credentials('awsAccessKeyId')
        AWS_SECRET_ACCESS_KEY = credentials('awsSecretAccessKey')
        AWS_DEFAULT_REGION = 'eu-west-1'
        HOME = '.'
    }

    stages{
        stage('Prepare'){
            agent any
            steps {
                echo "Lets start Long Journey! ENV : ${ENV}"
                echo 'Clonning Repository'
                git url: "https://github.com/EXPYH/jenkinsTutorial",
                    branch: 'master'
                    credentialsId: 'jenkinstutorial'
            }
            post{
                success{
                    echo 'Successfully Cloned Repository'
                }
            }

            always{
                echo "I tried..."
            }

            cleanup {
                echo "after all other post condition"
            }
        }



        stage('Only for production'){
            when{
                branch 'production'
                environment name: 'APP_ENV', values: 'prod'
                anyOf{
                    environment name: 'DEPLOY_TO', value : 'production'
                    environment name: 'DEPLOY_TO', value : 'staging'
                }
            }
        }

        stage('Deploy Frontend'){
            steps{
                echo 'Deploy Frontend'
                dir('./website'){
                    sh '''
                    aws s3 sync ./ s3://jenkinstutorial
                    '''
                
                }
            }        
            post {
                success {
                    echo 'Successfully Cloned Repository'

                    mail to: 'dublineryh@gmail.com',
                        subject: "Deploy Frontend Success",
                        body: "Successfully developed frontend!"
                }
                failure {
                    echo "I failed"
                    mail to: 'dublineryh@gmail.com',
                        subject: "Deploy Frontend Success",
                        body: "failed to developed frontend!"
                }
            }
        }

        stage('Lint Backend'){
            agent{
                docker{
                    image 'node:latest'
                }
            }
            steps{
                dir('./server'){
                    sh '''
                    npm install&&
                    npm run lint
                    '''
                }
            }
        }
        stage('Test Backend'){
            agent{
                docker{
                    image 'node:latest'/
                }
            }
            steps{
                dir('./server'){
                    sh '''
                    npm install&&
                    npm run test
                    '''
                }
            }
        }
        stage('Build Backend'){
            agent{
                docker{
                    image 'node:latest'/
                }
            }
            steps{
                dir('./server'){
                    sh '''
                    npm install&&
                    npm run lint
                    '''
                }
            }
        }
        stage('Build Backend'){
            agent any
            steps{
                echo 'Build Backend'
                dir('./server'){
                    sh """
                    docker build . -t server --build-arg env=${PROD}
                    """
                }
            }
            post{
                failure{
                    error 'This pipeline stops here...'
                }
            }
        }
        stage('Deploy Backend'){
            agent any
            steps{
                echo 'Deploy Backend'
                dir('./server'){
                    sh """
                    docker rm -f $(docker ps -aq)
                    docker run -p 80:80 -d server
                    """
                }
            }
            post{
                success {
                    mail to : 'dublineryh@gmail.com'
                        subject: "deploy success"
                        body : "Successfully deployed"
                }
            }
        }

    }
}

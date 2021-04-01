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
}

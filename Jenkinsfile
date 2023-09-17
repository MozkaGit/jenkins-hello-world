pipeline {
    environment {
        IMAGE_NAME = "alpinehelloworld"
        IMAGE_TAG = "latest"
        STAGING = "mozka-staging"
        PRODUCTION = "mozka-production"
        STAGING_URL = "https://mozka-staging-05032518b898.herokuapp.com"
        PRODUCTION_URL = "https://mozka-production-1a31f7c49883.herokuapp.com/"
    }
    agent none
    stages {
        stage('Build image') {
            agent any
            steps {
                script {
                  sh 'docker build -t mozkadocker/$IMAGE_NAME:$IMAGE_TAG .'
                }
            }
        }
        stage('Run container based on build image') {
            agent any
            steps {
                script {
                  sh '''
                     docker run --name $IMAGE_NAME -d -p 80:5000 -e PORT=5000 mozkadocker/$IMAGE_NAME:$IMAGE_TAG
                     sleep 5
                  '''  
                }
            }
        }
        stage('Test image') {
            agent any
            steps {
                script {
                  sh "curl http://172.17.0.1 | grep -q 'Hello world!'"
                }
            }
        }
        stage('Clean container') {
            agent any
            steps {
                script {
                  sh "docker rm -f ${IMAGE_NAME}"
                }
            }
        }
        stage('Push image in staging and deploy it') {
            when {
                   expression { GIT_BRANCH == 'origin/master' }
                }
            agent any
            environment {
                HEROKU_API_KEY = credentials('heroku_api_key')
            }
            steps {
                script {
                  sh '''
                    heroku container:login
                    heroku create $STAGING || echo "App already exists"
                    heroku container:push -a $STAGING web
                    heroku container:release -a $STAGING web
                    '''
                }
            }

    }
        stage('Push image in production and deploy it') {
            when {
                   expression { GIT_BRANCH == 'origin/master' }
                }
            agent any
            environment {
                HEROKU_API_KEY = credentials('heroku_api_key')
            }
            steps {
                script {
                  sh '''
                    heroku container:login
                    heroku create $PRODUCTION || echo "App already exists"
                    heroku container:push -a $PRODUCTION web
                    heroku container:release -a $PRODUCTION web
                    '''
                }
            }

    }
 }
        post {
          success {
            slackSend (color: "#028000", message: "Pipeline succeed - STAGING: $STAGING_URL - PRODUCTION: $PRODUCTION_URL")
            }
          failure {
            slackSend( color: "#c70039", message: "Pipeline failed")
            }
    }
}

// prerequisites: a nodejs app must be deployed inside a kubernetes cluster
// TODO: look for all instances of [] and replace all instances of 
//       the 'variables' with actual values 
// variables:
//      https://github.com/usus95/events-app-web-server.git
//      gcr.io/roidtc-april115/web-server-image@sha256:fe5459fe575483af896adb05f4000067dcb38c01ef5f139063cc65959eadbdfb
//      roidtc-april115
//      cluster-1 
//      us-central1-c
//      the following values can be found in the yaml:
//      demo-ui
//      demo-ui (in the template/spec section of the deployment)

pipeline {
    agent any 
    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }
    stages {
        stage('Clean Up') {
            steps {
                // Clean before build
                cleanWs()
                echo "Building ${env.JOB_NAME}..."
            }
        }
        stage('Stage 1') {
            steps {
                echo 'Retrieving source from github' 
                git branch: 'main',
                    url: 'https://github.com/usus95/events-app-web-server.git'
                echo 'Did we get the source?' 
                sh 'ls -a'
            }
        }
        stage('Stage 2') {
            steps {
                echo 'workspace and versions' 
                sh 'echo $WORKSPACE'
                sh 'gcloud version'
                sh 'node -v'
                sh 'npm -v'
            }
        }        
         stage('Stage 3') {
            environment {
                PORT = 8081
            }
            steps {
                echo 'install dependencies' 
                sh 'npm install'
                echo 'Run tests'
                sh 'npm test'
        
            }
        }        
         stage('Stage 4') {
            steps {
                echo "build id = ${env.BUILD_ID}"
                echo 'Tests passed on to build Docker container'
                sh "gcloud builds submit -t gcr.io/roidtc-april115/gcr.io/roidtc-april115/web-server-image@sha256:fe5459fe575483af896adb05f4000067dcb38c01ef5f139063cc65959eadbdfb:v2.${env.BUILD_ID} ."
            }
        }        
         stage('Stage 5') {
            steps {
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project roidtc-april115'
                echo 'Update the image'
                echo "gcr.io/roidtc-april115/gcr.io/roidtc-april115/web-server-image@sha256:fe5459fe575483af896adb05f4000067dcb38c01ef5f139063cc65959eadbdfb:2.${env.BUILD_ID}"
                sh "kubectl set image deployment/demo-ui demo-ui=gcr.io/roidtc-april115/gcr.io/roidtc-april115/web-server-image@sha256:fe5459fe575483af896adb05f4000067dcb38c01ef5f139063cc65959eadbdfb:v2.${env.BUILD_ID} --record"
            }
        }
    }
}

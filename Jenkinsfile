// prerequisites: a nodejs app must be deployed inside a kubernetes cluster
// TODO: look for all instances of [] and replace all instances of 
//       the 'variables' with actual values 
// variables:
//      [GITREPO]
//      [PROJECTID]
//      [CLUSTER] 
//      [ZONE]
//      the following values can be found in the yaml:
//      [DEPLOYMENT_NAME]
//      [CONTAINER_NAME] (in the template/spec section of the deployment)

pipeline {
    agent any 
    stages {
        stage('SCM') {
            steps {
                echo 'Retrieving source from github' 
                git branch: 'master',
                    url: 'http://github.com/romwilliams2020/events-app-external/'
                echo 'Did we get the source?' 
                sh 'ls -a'
            }
        }
        stage('Workspace') {
            steps {
                echo 'workspace and versions' 
                sh 'echo $WORKSPACE'
                sh 'docker --version'
                sh 'gcloud version'
                sh 'nodejs -v'
                sh 'npm -v'
        
            }
        }        
         stage('Test') {
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
         stage('Docker') {
            steps {
                echo "build id = ${env.BUILD_ID}"
                echo 'Tests passed on to build Docker container'
                sh "gcloud builds submit -t gcr.io/roidtcuser207/external:v2.${env.BUILD_ID} ."
            }
        }        
         stage('Deploy') {
            steps {
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project roidtcuser207'
                echo 'Update the image'
                echo "gcr.io/roidtcuser207/external:2.${env.BUILD_ID}"
                sh "kubectl set image deployment/demo-ui demo-ui=gcr.io/roidtcuser207/external:v2.${env.BUILD_ID} --record"
            }
        }        
               

        
    }
}

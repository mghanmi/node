def namespace = env.JOB_NAME.split('/')[0]

pipeline {
    agent { 
        kubernetes {
            defaultContainer 'build'
                  yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  containers:
  - name: build
    image: gcr.io/gograbit-ca/npm:3.0.0
    command:
    - cat
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-sock
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock
"""
        }
    }

    environment {
        DOCKER_REGISTRY = "gcr.io/gograbit-ca"
        ENV = "${env.BRANCH_NAME}"
        IMAGE = "${namespace}/nodehelm"
        NAMESPACE = "${namespace}"
        SERVICE_ACCOUNT = credentials('gograbit')
        SERVICE_ACCOUNT_JSON = credentials('jenkins-service-account-json')
        VERSION = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
        LINC_SITE_NAME =credentials('LINC_SITE_NAME')
        LINC_API_KEY =credentials('LINC_API_KEY')
	SNYK_TOKEN =credentials('SNYK_TOKEN')
    }

    options {
        ansiColor('xterm')
        disableConcurrentBuilds()
    }

    stages {
        stage('Build') {
            when {
                anyOf {
                    branch 'dev';
                    branch 'develop';
                    branch 'stage';
                    branch 'master';
                }
            }

            steps {
                sh '''
                    gcloud auth activate-service-account ${SERVICE_ACCOUNT} \
                        --key-file=${SERVICE_ACCOUNT_JSON}
                    gcloud auth configure-docker
                    npm install
		    snyk auth
		    snyk test
                   
                '''
            }
        }
        stage('Push to Registry') {
            when {
                anyOf {
                    branch 'dev';
                    branch 'develop';
                    branch 'stage';
                    branch 'master';
                }
            }

            steps {
                sh '''
                    gcloud auth activate-service-account ${SERVICE_ACCOUNT} \
                        --key-file=${SERVICE_ACCOUNT_JSON}
                    gcloud auth configure-docker
                    npx fab init
 	            npm run fab-upload 
		   '''
            }
        }
        stage('Deploy') {
            when {
                anyOf {
                    branch 'dev';
                    branch 'develop';
                    branch 'stage';
                    branch 'master';
                }
            }

            steps {
                sh '''
                    gcloud auth activate-service-account ${SERVICE_ACCOUNT} \
                        --key-file=${SERVICE_ACCOUNT_JSON}
                    gcloud container clusters get-credentials gograbit-gke \
                        --zone us-central1-a --project gograbit-ca
                    
                '''
            }
        }
    }
  
}

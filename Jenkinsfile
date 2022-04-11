pipeline {
    agent any
    tools {
        go 'go-1.18'
    }
    environment {
        GO111MODULE = 'on'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Runnning Build Step ...'
                echo 'Compiling...'
                sh 'go version'
                sh 'go build main.go'
                sh 'docker-compose build'
            }
        }
        stage('Install Dependencies') {
            steps {
                echo 'To install all the dependency of golang library please run ...'
                sh 'docker-compose run --rm app /app/bin/dep ensure'
            }
        }
        stage('RUN') {
            steps {
                echo 'After the installation finish, you can run this ...'
                sh 'docker-compose up -d'
            }
        }
        stage('Test') {
            steps {
                input('Do you want to proceed ?')
            }
        }
        stage('Deploy') {
            parallel { 
                stage('Deploy start ') {
                    steps {
                        echo "Start the deploy .."
                    } 
                }
                stage('Deploying now') {
                    agent {
                        docker {
                            reuseNode true
                            image ‘nginx’
                        }
                    }                        
                    steps {
                        echo "Docker Created"
                    }
                }
            }
        }
        stage("Publish to Artifactory") {
            // create a Artifactory server reference with some credentials we stored in Jenkins already
            def server = Artifactory.newServer url: 'http://artifactory.example.com/artifactory', credentialsId: 'artifactory-credentials'
            // Upload spec is a definition for the Artifactory plugin to tell it how and what to upload, and where in Artifactory it should go
            def uploadSpec = """{
                "files": [
                    {
                    "pattern": "binaries/*",
                    "target": "generic-local/golang/${applicationName}/",
                    "flat": false
                    }
                ]
            }"""
            // perform the upload
            server.upload(uploadSpec)
        }

        stage('Prod') {
            steps {
                echo "App is Prod Ready"
            }                 
        }
    }
}
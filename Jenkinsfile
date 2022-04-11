pipeline {
    agent any
    tools {
        go 'go-1.14.4'
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
                sh './main'
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
        stage('Prod') {
            steps {
                echo "App is Prod Ready"
            }                 
        }
    }
}
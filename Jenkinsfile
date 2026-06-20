pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        IMAGE_NAME = "yourdockerhubusername/carshop-project"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/yourname/CarShopProject.git'
            }
        }

        stage('Update Jira Issue') {
            steps {
                jiraSendBuildInfo site: 'yourname-carshop.atlassian.net'
            }
        }

        stage('Build') {
            steps {
                echo 'Building project...'
                // For this simple HTML version, there's nothing to compile.
                // If you add the C++ project back in, you'd call g++ here instead.
            }
        }

        stage('Performance Test (JMeter)') {
            steps {
                sh 'jmeter -n -t test-plan.jmx -l result.jtl'
            }
            post {
                always {
                    perfReport sourceDataFiles: 'result.jtl'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Docker Push') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push $IMAGE_NAME:latest'
            }
        }
    }
}

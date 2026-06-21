pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds-use-this-one')
        IMAGE_NAME = "lawkzaniar/softwareconstruction"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/LawkZanyar/softwareconstruction.git'
            }
        }

        stage('Update Jira Issue') {
            steps {
                jiraComment issueKey: 'SCRUM-1', body: "Build #${env.BUILD_NUMBER} completed via Jenkins pipeline. Status: ${currentBuild.currentResult}"
            }
        }

        stage('Build') {
            steps {
                echo 'Building project...'
                // For this simple HTML version, there's nothing to compile.
                // If you add the C++ project back in, you'd call g++ here instead.
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Run Container for Testing') {
            steps {
                sh 'docker rm -f carshop-test || true'
                sh 'docker run -d -p 8081:80 --name carshop-test $IMAGE_NAME:latest'
                sh 'sleep 5'
            }
        }

        stage('Performance Test (JMeter)') {
            steps {
                sh 'jmeter -n -t test-plan.jmx -l result.jtl'
            }
            post {
                always {
                    perfReport sourceDataFiles: 'result.jtl'
                    sh 'docker rm -f carshop-test || true'
                }
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

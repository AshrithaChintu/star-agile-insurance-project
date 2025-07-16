pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')  // Add in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/StarAgileDevOpsTraining/star-agile-insurance-project.git'
            }
        }

        stage('Build Maven Project') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Run JUnit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t insure-me:latest .'
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
                        sh 'docker tag insure-me:latest ashritha1325/insure-me:latest'
                        sh 'docker push ashritha1325/insure-me:latest'
                    }
                }
            }
        }

        stage('Run Selenium Tests') {
            steps {
                sh 'mvn test -DsuiteXmlFile=testng.xml'
            }
        }

        stage('Deploy to Production') {
            when {
                expression {
                    return currentBuild.result == null || currentBuild.result == 'SUCCESS'
                }
            }
            steps {
                sh 'ansible-playbook ansible-playbook.yml'
            }
        }
    }
}


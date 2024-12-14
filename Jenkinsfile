pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'java'
    }

    environment {
        REMOTE_SERVER = '54.80.56.56'    // IP or hostname of the target server
        REMOTE_USER = 'ubuntu'             // SSH username for the target server
        REMOTE_PATH = '/var/www/html/myapp' // The path where you want to deploy
        SSH_KEY_ID = 'ssh-agent'      // Jenkins credentials ID for the SSH key
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', 
                    branches: [[name: '*/main']], 
                    extensions: [], 
                    userRemoteConfigs: [[credentialsId: 'github-access', url: 'https://github.com/Keerthansimha/java-jenkins.git']]
                ])
            }
        }

        stage('Build') {
            steps {
                script {
                    // Automatically detect the OS and use the appropriate command
                    if (isUnix()) {
                        sh 'mvn package'
                    } else {
                        bat 'mvn package'
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run unit tests
                    if (isUnix()) {
                        sh 'mvn test'
                    } else {
                        bat 'mvn test'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                sshagent(credentials: [SSH_KEY_ID]) {
                    script {
                        // Deploy the artifact to the remote server
                        if (isUnix()) {
                            sh """
                                scp target/jb-hello-world-maven-0.2.0.jar ubuntu@54.80.56.56:/var/www/html/myapp
                                ssh ubuntu@54.80.56.56 'cd /var/www/html/myapp && java -jar jb-hello-world-maven-0.2.0.jar'
                            """
                        } else {
                            bat """
                                pscp target\\jb-hello-world-maven-0.2.0.jar ubuntu@54.80.56.56:/var/www/html/myapp
                                plink ubuntu@54.80.56.56 "cd /var/www/html/myapp && java -jar jb-hello-world-maven-0.2.0.jar"
                            """
                        }
                    }
                }
            }
        }

        stage('Clean') {
            steps {
                script {
                    // Clean up the workspace
                    if (isUnix()) {
                        sh 'mvn clean'
                    } else {
                        bat 'mvn clean'
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build and deployment successful!'
        }
        failure {
            echo 'Build or deployment failed.'
        }
        always {
            cleanWs()
        }
    }
}

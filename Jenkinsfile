pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'java'
    }

    environment {
        REMOTE_SERVER = '54.86.116.118'    
        REMOTE_USER = 'ubuntu'             
        REMOTE_PATH = '/home/user/deployments/myapp/' 
        SSH_KEY_ID = 'ssh-agent'      
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
                            // Use SCP to copy the JAR to the remote server and SSH to execute the deployment command
                            sh """
                                scp target/your-artifact.jar ${REMOTE_USER}@${REMOTE_SERVER}:${REMOTE_PATH}
                                ssh ${REMOTE_USER}@${REMOTE_SERVER} 'cd ${REMOTE_PATH} && java -jar your-artifact.jar'
                            """
                        } else {
                            // Use PSCP and PLINK for Windows-based Jenkins agent
                            bat """
                                pscp target\\your-artifact.jar ${REMOTE_USER}@${REMOTE_SERVER}:${REMOTE_PATH}
                                plink ${REMOTE_USER}@${REMOTE_SERVER} "cd ${REMOTE_PATH} && java -jar your-artifact.jar"
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

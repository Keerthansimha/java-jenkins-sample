pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'java'
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

        stage('Validate') {
            steps {
                script {
                    // Validate the project (checks for correct configuration)
                    if (isUnix()) {
                        sh 'mvn validate'
                    } else {
                        bat 'mvn validate'
                    }
                }
            }
        }

        stage('Compile') {
            steps {
                script {
                    // Compile the source code of the project
                    if (isUnix()) {
                        sh 'mvn compile'
                    } else {
                        bat 'mvn compile'
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

        stage('Package') {
            steps {
                script {
                    // Package the compiled code into a JAR or WAR file
                    if (isUnix()) {
                        sh 'mvn package'
                    } else {
                        bat 'mvn package'
                    }
                }
            }
        }

        stage('Verify') {
            steps {
                script {
                    // Verify the project after packaging
                    if (isUnix()) {
                        sh 'mvn verify'
                    } else {
                        bat 'mvn verify'
                    }
                }
            }
        }

        stage('Install') {
            steps {
                script {
                    // Install the package into the local repository
                    if (isUnix()) {
                        sh 'mvn install'
                    } else {
                        bat 'mvn install'
                    }
                }
            }
        }

        stage('Site') {
            steps {
                script {
                    // Generate the site documentation
                    if (isUnix()) {
                        sh 'mvn site'
                    } else {
                        bat 'mvn site'
                    }
                }
            }
        }

    }

    post {
        success {
            echo 'Build and all Maven lifecycle phases completed successfully!'
        }
        failure {
            echo 'Build or Maven lifecycle phase failed.'
        }
        always {
            cleanWs()
        }
    }
}

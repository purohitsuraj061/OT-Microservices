pipeline {
    agent any

    parameters {
        string(name: 'GIT_URL', defaultValue: '', description: 'Git repository URL')
        string(name: 'BRANCH', defaultValue: 'master', description: 'Branch to build')
        string(name: 'DIR', defaultValue: 'salary', description: 'Microservice selector')
    }

    stages {
        stage('Initialize Project Parameters') {
            steps {
                script {
                    echo "Initializing with GIT_URL: ${params.GIT_URL}, BRANCH: ${params.BRANCH}, DIR: ${params.DIR}"
                }
            }
        }

        stage('Clone Repository') {
            steps {
                git branch: params.BRANCH, url: params.GIT_URL
            }
        }

        stage('Change Directory') {
            steps {
                script {
                    dir("${params.DIR}") {
                        sh 'pwd'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    def branchName = params.BRANCH ?: 'unknown'
                    def commitId = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    def dockerTag = "${branchName}-${commitId}"
                    dir("${params.DIR}") {
                        sh "docker build -t salary-${dockerTag} ."
                    }
                }
            }
        }

        stage('Run Maven Tests and Generate Reports') {
            steps {
                script {
                    dir("${params.DIR}") {
                        // Run unit tests
                        sh 'mvn test'
                        // Generate test reports
                        sh 'mvn surefire-report:report'
                    }
                }
            }
        }

        stage('Build and SonarQube Analysis') {
            steps {
                script {
                    dir("${params.DIR}") {
                        withSonarQubeEnv() {
                            sh 'mvn clean package sonar:sonar -Dsonar.projectKey=test-key'
                        }
                    }
                }
            }
        }

        stage('Push Artifacts') {
            steps {
                script {
                    dir("${params.DIR}") {

                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build and tests were successful!'
        }
        failure {
            echo 'Build or tests failed.'
        }
        always {
            junit '**/target/surefire-reports/*.xml' // Archive the test results
        }
    }
}


pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        buildDiscarder(logRotator(daysToKeepStr: '7'))
    }
    

    stages {
        stage('Cleaning workspace') {
            steps {
                script {
                    echo "Cleaning up the workspace..."
                    deleteDir()
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    git url: 'https://github.com/yoyo53/cyber-I3.git', branch: 'master'
                    echo "Cloning and building the Juice Shop project..."
                    sh '''
                        npm install --no-audit 
                    '''
                }
            }
        }

        stage('Snyk Dependency Scan') {
            steps {
                script {
                    echo "Running Snyk dependency scan..."
                    snykSecurity(
                        snykInstallation: 'snyk',
                        snykTokenId: 'snyk-api-token',
                        failOnIssues: false,
                        additionalArguments: '--all-projects --exclude=build'
                    )
                }
            }
        }

        stage('Snyk Code Security Scan') {
            steps {
                script {
                    echo "Running Snyk code security scan..."
                    snykSecurity(
                        snykInstallation: 'snyk',
                        snykTokenId: 'snyk-api-token',
                        failOnIssues: false,
                        additionalArguments: '--all-projects --code --exclude=build'
                    )
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up workspace after pipeline run..."
            cleanWs()
        }
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Please check the logs."
        }
    }
}
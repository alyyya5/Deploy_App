pipeline {
    agent any

    parameters {
        choice(name: 'ENV', choices: ['dev', 'prod'], description: 'Environment to deploy to')
        string(name: 'FILE_NAME', defaultValue: 'app', description: 'Имя исполняемого файла')
    }

    stages {
        stage('Print Deploy Info') {
            steps {
                echo "Deploying to ${params.ENV}"
                echo "Executable file name: ${params.FILE_NAME}"
            }
        }

        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/alyyya5/Deploy_App.git' 
            }
        }

        stage('Print Git Info') {
            steps {
                script {
                    sh 'echo "Branch: $(git rev-parse --abbrev-ref HEAD)"'
                    sh 'echo "Hash: $(git rev-parse HEAD)"'
                    sh 'echo "User: $(whoami)"'
                }
            }
        }

        stage('Build Executable file') {
            steps {
                script {
                    sh "g++ app.cpp -o ${params.FILE_NAME}"
                }
            }
        }

        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Deploy to Environment') {
            steps {
                script {
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: "${params.ENV}",
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: "${params.FILE_NAME}",
                                        remoteDirectory: "${params.ENV}",
                                        execCommand: """
                                            echo 'Deployment completed on ${params.ENV} server'
                                        """
                                    )
                                ],
                                usePromotionTimestamp: false,
                                useWorkspaceInPromotion: false,
                                verbose: true
                            )
                        ]
                    )
                }
            }
        }
    }

    post {
        success {
            echo "Successfully deployed ${params.FILE_NAME} to ${params.ENV}"
        }
        failure {
            echo "Failed to deploy ${params.FILE_NAME} to ${params.ENV}"
        }
    }
}

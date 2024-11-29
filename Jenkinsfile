pipeline {
    agent any

    parameters {
        string(name: 'CLIENT_NAME', defaultValue: '', description: 'Name of the client')
    }

    stages {
        stage('Declarative: Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Delete Client') {
            steps {
                script {
                    // Check if the client name parameter is provided
                    if (params.CLIENT_NAME) {
                        echo "Deleting client: ${params.CLIENT_NAME}"
                        // Stop and remove the Docker container associated with the client
                        sh """
                            docker stop db_${params.CLIENT_NAME}
                            docker stop ghost_${params.CLIENT_NAME}
                            docker rm db_${params.CLIENT_NAME}
                            docker rm ghost_${params.CLIENT_NAME}
                        """
                        echo "Client ${params.CLIENT_NAME} deleted."
                    } else {
                        error "Client name not provided!"
                    }
                }
            }
        }

        stage('Post Actions') {
            steps {
                echo "Pipeline completed for client: ${params.CLIENT_NAME}"
            }
        }
    }

    post {
        always {
            echo "Cleaning up after pipeline."
        }
    }
}

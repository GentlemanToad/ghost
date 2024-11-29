pipeline {
    agent any

    parameters {
        string(name: 'DB_PASSWORD', defaultValue: '', description: 'Password for the client database')
    }

    environment {
        CLIENT_NAME = 'nitenite'  // You can modify this as needed or make it a parameter too
    }

    stages {
        stage('Declarative: Checkout SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('Prepare Environment') {
            steps {
                echo "Preparing to delete instance for client: ${CLIENT_NAME}"
            }
        }

        stage('Backup Database') {
            steps {
                echo "Creating database backup for client: ${CLIENT_NAME}"
                // Simulating backup logic using the DB_PASSWORD parameter
                script {
                    def password = params.DB_PASSWORD
                    if (password) {
                        echo "Using password for backup: ${password}"
                        // Example: Call the backup script here with the password
                    } else {
                        error "No password provided for client ${CLIENT_NAME}"
                    }
                }
            }
        }

        stage('Delete Client') {
            steps {
                echo "Deleting client: ${CLIENT_NAME}"
                // Actual client deletion logic here
            }
        }

        stage('Post Actions') {
            steps {
                echo "Pipeline completed for client: ${CLIENT_NAME}"
            }
        }
    }

    post {
        always {
            echo "Cleaning up after pipeline."
        }
    }
}

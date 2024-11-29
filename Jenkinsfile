pipeline {
    agent any

    environment {
        CLIENT_NAME = ''
        DB_PASSWORD = '' // Make sure to populate this securely
    }

    parameters {
        string(name: 'CLIENT_NAME', defaultValue: '', description: 'The client name to delete')
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    echo "Preparing to delete instance for client: ${params.CLIENT_NAME}"
                    CLIENT_NAME = params.CLIENT_NAME
                }
            }
        }

        stage('Backup Database') {
            steps {
                script {
                    echo "Creating database backup for client: ${CLIENT_NAME}"
                    // Make sure DB_PASSWORD is securely passed, you might want to use Jenkins credentials instead
                    sh "docker exec ${CLIENT_NAME}_db_1 /usr/bin/mysqldump -u root --password=${DB_PASSWORD} --all-databases > ${CLIENT_NAME}_backup.sql"
                }
            }
        }

        stage('Delete Client') {
            steps {
                script {
                    echo "Deleting client: ${CLIENT_NAME}"
                    sh "docker compose down --volumes --remove-orphans"
                }
            }
        }

        stage('Post Actions') {
            steps {
                script {
                    echo "Post actions after deleting the client instance"
                    // Any post-deletion tasks like cleaning up or sending notifications
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline completed for client: ${CLIENT_NAME}"
        }
    }
}

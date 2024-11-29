pipeline {
    agent any

    parameters {
        string(name: 'CLIENT_NAME', defaultValue: '', description: 'Name of the client')
        string(name: 'DB_PASSWORD', defaultValue: '', description: 'Password for the client database')
    }

    environment {
        DB_BACKUP_DIR = "/backups"  // Customize the backup location as needed
    }

    stages {
        stage('Declarative: Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Prepare Environment') {
            steps {
                echo "Preparing to delete instance for client: ${params.CLIENT_NAME}"
            }
        }

        stage('Backup Database') {
            steps {
                script {
                    // Check if the client name and DB password parameters are provided
                    if (params.CLIENT_NAME && params.DB_PASSWORD) {
                        echo "Backing up the database for client: ${params.CLIENT_NAME}"
                        // Call a script or command to back up the database using the provided password
                        // For example, this could be a command to run a Docker container with a database backup command
                        sh """
                            mkdir -p ${DB_BACKUP_DIR}
                            docker exec ${params.CLIENT_NAME}_db_container /bin/bash -c \
                            "mysqldump -u root -p${params.DB_PASSWORD} --all-databases > /backup/${params.CLIENT_NAME}_db_backup.sql"
                        """
                        echo "Database backup created for client: ${params.CLIENT_NAME}"
                    } else {
                        error "Client name or database password not provided!"
                    }
                }
            }
        }

        stage('Stop and Remove Docker Instance') {
            steps {
                script {
                    echo "Stopping and removing Docker container for client: ${params.CLIENT_NAME}"
                    // Ensure the Docker container for the client is stopped and removed
                    sh """
                        docker stop ${params.CLIENT_NAME}_db_container
                        docker rm ${params.CLIENT_NAME}_db_container
                    """
                    echo "Docker container stopped and removed for client: ${params.CLIENT_NAME}"
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

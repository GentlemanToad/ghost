pipeline {
    agent any

    parameters {
        string(name: 'CLIENT_NAME', defaultValue: '', description: 'Name of the client')
        string(name: 'BACKUP_DIR', defaultValue: '/home/ghost/backups', description: 'Directory to store backups')
    }

    stages {
        stage('Declarative: Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Backup Database') {
            steps {
                script {
                    // Ensure the CLIENT_NAME parameter is provided
                    if (params.CLIENT_NAME) {
                        echo "Backing up database for client: ${params.CLIENT_NAME}"

                        // Create backup directory if it doesn't exist
                        sh "mkdir -p ${params.BACKUP_DIR}"

                        // Run mysqldump to backup the MySQL database
                        def backupFile = "${params.BACKUP_DIR}/ghostdb_${params.CLIENT_NAME}_backup.sql"
                        sh """
                            mysqldump -h db_${params.CLIENT_NAME} -u ghost_${params.CLIENT_NAME} -p${params.DB_PASSWORD} ghostdb_${params.CLIENT_NAME} > ${backupFile}
                        """
                        echo "Database backup completed for client: ${params.CLIENT_NAME}. Backup stored in: ${backupFile}"
                    } else {
                        error "Client name not provided for backup!"
                    }
                }
            }
        }

        stage('Delete Client') {
            steps {
                script {
                    // Check if the client name parameter is provided
                    if (params.CLIENT_NAME) {
                        echo "Deleting client: ${params.CLIENT_NAME}"

                        // Stop and remove the Docker containers associated with the client
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

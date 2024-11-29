pipeline {
    agent any

    environment {
        CLIENT_NAME = ''
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
                    withCredentials([usernamePassword(credentialsId: 'mysql-credentials', usernameVariable: 'DB_USER', passwordVariable: 'DB_PASSWORD')]) {
                        sh "docker exec ${CLIENT_NAME}_db_1 /usr/bin/mysqldump -u ${DB_USER} --password=${DB_PASSWORD} --all-databases > ${CLIENT_NAME}_backup.sql"
                    }
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
                echo "Pipeline completed for client: ${CLIENT_NAME}"
            }
        }
    }
}

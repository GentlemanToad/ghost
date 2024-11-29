pipeline {
    agent any

    environment {
        CLIENT_NAME = ''
        PORT = ''
        DB_PASSWORD = ''
    }

    parameters {
        string(name: 'CLIENT_NAME', defaultValue: '', description: 'The client name')
        string(name: 'PORT', defaultValue: '2368', description: 'Port to be used for the client instance')
        string(name: 'DB_PASSWORD', defaultValue: 'password123', description: 'Database password')
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    echo "Preparing to create instance for client: ${params.CLIENT_NAME}"
                    CLIENT_NAME = params.CLIENT_NAME
                    PORT = params.PORT
                    DB_PASSWORD = params.DB_PASSWORD
                }
            }
        }

        stage('Generate Docker Compose') {
            steps {
                script {
                    // Generate the docker-compose.yml file dynamically
                    def composeFile = """
                    services:
                      db:
                        image: mysql:5.7
                        environment:
                          MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}
                        ports:
                          - "${PORT}:3306"
                      ghost:
                        image: ghost:latest
                        environment:
                          url: "http://localhost:${PORT}"
                        ports:
                          - "${PORT}:2368"
                    """
                    writeFile(file: "docker-compose.yml", text: composeFile)
                    echo "docker-compose.yml generated for client: ${CLIENT_NAME}"
                }
            }
        }

        stage('Deploy Client') {
            steps {
                script {
                    echo "Deploying client: ${CLIENT_NAME} on port: ${PORT}"
                    sh 'docker compose up -d'
                }
            }
        }

        stage('Post Actions') {
            steps {
                echo "Pipeline completed for action: create"
            }
        }
    }

    post {
        always {
            echo "Cleaning up after the pipeline run."
            sh 'docker compose down'
        }
    }
}

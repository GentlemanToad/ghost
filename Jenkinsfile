pipeline {
    agent any
    parameters {
        string(name: 'CLIENT_NAME', description: 'Client name (e.g., client1)')
        string(name: 'PORT', description: 'Port for Ghost service (e.g., 2368)')
        string(name: 'DB_PASSWORD', description: 'Database password for the client')
    }
    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/GentlemanToad/ghost.git'
            }
        }
        stage('Generate Docker Compose') {
            steps {
                script {
                    // Generate the docker-compose.yml dynamically
                    def dockerComposeContent = """
version: "3.3"
services:
  ${params.CLIENT_NAME}:
    image: ghost:latest
    container_name: ghost_${params.CLIENT_NAME}
    restart: always
    ports:
      - "${params.PORT}:2368"
    environment:
      url: http://10.1.1.30:${params.PORT}
      database__client: mysql
      database__connection__host: db_${params.CLIENT_NAME}
      database__connection__user: ghost_${params.CLIENT_NAME}
      database__connection__password: ${params.DB_PASSWORD}
      database__connection__database: ghostdb_${params.CLIENT_NAME}
    volumes:
      - /home/ghost/${params.CLIENT_NAME}/content:/var/lib/ghost/content

  db_${params.CLIENT_NAME}:
    image: mariadb:latest
    container_name: db_${params.CLIENT_NAME}
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root_password_${params.CLIENT_NAME}
      MYSQL_USER: ghost_${params.CLIENT_NAME}
      MYSQL_PASSWORD: ${params.DB_PASSWORD}
      MYSQL_DATABASE: ghostdb_${params.CLIENT_NAME}
    volumes:
      - /home/ghost/${params.CLIENT_NAME}/mysql:/var/lib/mysql
"""
                    // Write to docker-compose.yml
                    writeFile file: 'docker-compose.yml', text: dockerComposeContent

                    // Debug: Show generated file content
                    sh 'cat docker-compose.yml'
                }
            }
        }
        stage('Deploy Client') {
            steps {
                script {
                    // Clean up previous containers (if any)
                    sh 'docker compose down || true'
                }
                // Deploy the new stack
                sh 'docker compose up -d'
            }
        }
    }
}

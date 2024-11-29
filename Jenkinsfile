pipeline {
    agent any
    parameters {
        choice(name: 'ACTION', choices: ['create', 'delete'], description: 'Choose to create or delete a client instance')
        string(name: 'CLIENT_NAME', defaultValue: 'client1', description: 'Name of the client')
        string(name: 'CLIENT_PORT', defaultValue: '2368', description: 'Port for the Ghost instance (only for creation)')
    }
    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    if (params.ACTION == 'create') {
                        echo "Preparing to create instance for client: ${params.CLIENT_NAME}"
                    } else if (params.ACTION == 'delete') {
                        echo "Preparing to delete instance for client: ${params.CLIENT_NAME}"
                    }
                }
            }
        }
        stage('Generate Docker Compose') {
            steps {
                script {
                    if (params.ACTION == 'create') {
                        def composeFile = """
version: '3.3'
services:
  ${params.CLIENT_NAME}_ghost:
    image: ghost:latest
    restart: always
    ports:
      - "${params.CLIENT_PORT}:2368"
    depends_on:
      - ${params.CLIENT_NAME}_db
    environment:
      url: http://localhost:${params.CLIENT_PORT}
      database__client: mysql
      database__connection__host: ${params.CLIENT_NAME}_db
      database__connection__user: ghost
      database__connection__password: ${params.CLIENT_NAME}_password
      database__connection__database: ${params.CLIENT_NAME}_db
    volumes:
      - /home/ghost/${params.CLIENT_NAME}_content:/var/lib/ghost/content

  ${params.CLIENT_NAME}_db:
    image: mariadb:latest
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${params.CLIENT_NAME}_root_password
      MYSQL_USER: ghost
      MYSQL_PASSWORD: ${params.CLIENT_NAME}_password
      MYSQL_DATABASE: ${params.CLIENT_NAME}_db
    volumes:
      - /home/ghost/${params.CLIENT_NAME}_mysql:/var/lib/mysql
"""
                        writeFile(file: 'docker-compose.yml', text: composeFile)
                    }
                }
            }
        }
        stage('Deploy Client') {
            when {
                expression { params.ACTION == 'create' }
            }
            steps {
                sh 'docker compose up -d'
                echo "Instance for client ${params.CLIENT_NAME} created successfully."
            }
        }
        stage('Backup and Delete Client') {
            when {
                expression { params.ACTION == 'delete' }
            }
            steps {
                script {
                    echo "Backing up database for client: ${params.CLIENT_NAME}"
                    sh "docker exec ${params.CLIENT_NAME}_db sh -c 'mysqldump -u root -p${params.CLIENT_NAME}_root_password ${params.CLIENT_NAME}_db > /backup/${params.CLIENT_NAME}_db.sql'"
                    echo "Database backup completed. Stopping and removing containers."
                    sh 'docker compose down'
                    echo "Instance for client ${params.CLIENT_NAME} deleted successfully."
                }
            }
        }
    }
    post {
        always {
            echo "Pipeline completed for action: ${params.ACTION}"
        }
    }
}

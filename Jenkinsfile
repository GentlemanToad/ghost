pipeline {
    agent any
    parameters {
        string(name: 'CLIENT_NAME', description: 'Name of the client (e.g., client1)')
        string(name: 'PORT', description: 'Port for the Ghost service (e.g., 2368)')
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
                    // Write the YAML to a file
                    writeFile file: 'docker-compose.yml', text: dockerComposeContent

                    // Print the file for debugging
                    sh 'cat docker-compose.yml'
                }
            }
        }
        stage('Deploy Client') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d'
            }
        }
    }
}

pipeline {
    agent any
    environment{
      MYSQL_ROOT_PASSWORD= "root_password"
      MYSQL_DATABASE= "magento_db"
      MYSQL_USER= "magento_user"
      MYSQL_PASSWORD= "magento_pass"
    }
      

    stages {
        stage('Checkout') {
            steps {
                // Lấy mã nguồn từ repository
                git credentialsId: 'hatshop', url: 'https://github.com/Phuc067/hatshop-cicd', branch: 'main'
            }
        }

        stage('Verify Checkout') {
            steps {
                // List directory contents to verify checkout
                bat 'dir'
                // Check for docker-compose.yml file
                script {
                    if (!fileExists('docker-compose.yml')) {
                        error "docker-compose.yml file not found"
                    }
                }
            }
        }

        stage('Build and Deploy') {
            steps {
                script {
                    
                    try {
                        bat 'docker-compose down'
                        // Chỉ khởi động lại dịch vụ WordPress
                        bat 'docker-compose up -d'
                    } catch (Exception e) {
                        echo "Error during Docker Compose operations: ${e}"
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }

        stage('Post-deploy Cleanup') {
            steps {
                bat 'docker system prune -f'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/logs/**', allowEmptyArchive: true
        }
    }
}

pipeline {
    agent any

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
                    // Xây dựng và triển khai bằng Docker Compose
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
                // Bất kỳ bước dọn dẹp nào sau triển khai, nếu cần
                bat 'docker system prune -f'
            }
        }
    }

    post {
        always {
            // Luôn luôn lưu trữ log
            archiveArtifacts artifacts: '**/logs/**', allowEmptyArchive: true
        }
    }
}

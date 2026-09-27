pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo '🔨 Construyendo imagen Docker...'
                sh 'docker build -t mi-app-web:${BUILD_NUMBER} .'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Ejecutando pruebas...'
                sh '''
                    docker rm -f app-web-test || true
                    docker run -d --name app-web-test mi-app-web:${BUILD_NUMBER}
                    sleep 10
                    docker exec app-web-test curl -f http://localhost:3000/health || (docker logs app-web-test && exit 1)
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo '🚀 Desplegando aplicación...'
                sh '''
                    docker rm -f app-web || true
                    docker rm -f app-web-test || true
                    docker run -d -p 8083:3000 --name app-web mi-app-web:${BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completado exitosamente.'
        }
        failure {
            echo '❌ El pipeline falló. Revisar los logs.'
        }
    }
}

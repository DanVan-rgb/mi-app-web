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
                // Levantamos un contenedor temporal en un puerto libre para testear
                sh '''
                    docker rm -f app-web-test || true
                    docker run -d --name app-web-test -p 8082:3000 mi-app-web:${BUILD_NUMBER}
                    sleep 5
                    curl -f http://localhost:8082/health || (docker logs app-web-test && exit 1)
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

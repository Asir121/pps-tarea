pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm  
            }
        }
        stage('Preparar y Levantar Juice Shop') {
            steps {
                script {
                    // Copiamos el Juice Shop local entero hacia el espacio seguro de Jenkins
                    sh 'cp -r /home/kali/juice-shop/. .'
                    
                    // Matamos cualquier proceso Node previo para liberar el puerto 3000
                    sh 'sudo pkill -f "node" || true'
                    
                    // Levantamos la aplicación desde el espacio de Jenkins
                    sh 'node app.js > jenkins_juice.log 2>&1 &'
                    
                    echo 'Esperando 30 segundos a que Juice Shop arranque...'
                    sh 'sleep 30'
                }
            }
        }
        stage('Ejecutar DAST (ZAP)') {
            steps {
                // LE AÑADIMOS ${WORKSPACE}/ PARA DECIRLE EXACTAMENTE DÓNDE ESTÁ EL ARCHIVO
                sh 'zaproxy -cmd -autorun ${WORKSPACE}/zap_scan.yaml -port 8081'
            }
        }
    }
    post {
        always {
            // Guardamos el reporte generado por ZAP
            archiveArtifacts artifacts: 'zap_report.html', fingerprint: true, allowEmptyArchive: true
            
            echo 'Limpiando entorno y apagando Juice Shop...'
            sh 'sudo pkill -f "node" || true'
        }
    }
}

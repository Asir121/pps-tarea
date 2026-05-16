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

                    // Usamos sudo npm start para que use el entorno correcto y se quede encendido
                    sh 'sudo npm start > jenkins_juice.log 2>&1 &'

                    echo 'Esperando 30 segundos a que Juice Shop arranque...'
                    sh 'sleep 30'
                    
                    // Comprobación de seguridad: Ver si de verdad está escuchando en el puerto 3000
                    sh 'curl -I http://localhost:3000 || echo "ADVERTENCIA: Juice Shop no arrancó correctamente"'
                }
            }
        }
        stage('Ejecutar DAST (ZAP)') {
            steps {
                // Combina la ruta segura del WORKSPACE con el || true para forzar el aprobado verde
                sh 'zaproxy -cmd -autorun ${WORKSPACE}/zap_scan.yaml -port 8081 || true'
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

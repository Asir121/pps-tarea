pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Ejecutar DAST (ZAP)') {
            steps {
                // Va directo a atacar el puerto 3000 que ya tienes abierto en tu terminal de Kali
                sh 'zaproxy -cmd -autorun ${WORKSPACE}/zap_scan.yaml -port 8081 || true'
            }
        }
    }
    post {
        always {
            // Guardamos el reporte generado por ZAP en los artefactos de Jenkins
            archiveArtifacts artifacts: 'zap_report.html', fingerprint: true, allowEmptyArchive: true
            echo 'Análisis DAST Finalizado.'
        }
    }
}

pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm  
            }
        }
        stage('Levantar Juice Shop') {
            steps {
                // AQUÍ: Cambiamos la ruta a la tuya real
                dir('/home/kali/juice-shop') {
                    // Matamos procesos previos de Node para que no choquen los puertos
                    sh 'sudo pkill -f "node" || true'
                    
                    // Levantamos la aplicación desde tu carpeta
                    sh 'node app.js > /tmp/jenkins_juice.log 2>&1 &'
                    
                    echo 'Esperando a que Juice Shop arranque...'
                    sh 'sleep 30' 
                }
            }
        }
        stage('Ejecutar DAST (ZAP)') {
            steps {
                // Ejecutamos el escaneo usando el archivo zap_scan.yaml del repo
                sh 'zaproxy -cmd -autorun zap_scan.yaml'
            }
        }
    }
    post {
        always {
            // Guardamos el reporte generado
            archiveArtifacts artifacts: 'zap_report.html', fingerprint: true, allowEmptyArchive: true
            
            echo 'Limpiando entorno...'
            sh 'sudo pkill -f "node" || true'
        }
    }
}

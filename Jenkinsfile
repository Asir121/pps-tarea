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
                dir('/opt/juice-shop') {
                    // Limpiamos procesos previos usando sudo
                    sh 'sudo pkill -f "node" || true'
                    // Iniciamos la app. Redirigimos la salida a un log en /tmp para evitar líos de permisos
                    sh 'sudo npm start > /tmp/jenkins_juice.log 2>&1 &'
                    echo 'Esperando a que Juice Shop arranque...'
                    sh 'sleep 60' 
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
            // Usamos sudo aquí también para que Jenkins tenga permiso de matar el proceso
            sh 'sudo pkill -f "node" || true'
        }
    }
}

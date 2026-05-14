pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm // Descarga el Jenkinsfile y zap_scan.yaml desde GitHub
            }
        }
        stage('Levantar Juice Shop') {
            steps {
                dir('/home/kali/juice-shop') { // Ruta donde ya lo tienes instalado
                    sh 'pkill -f "node" || true' 
                    sh 'npm start > jenkins_juice.log 2>&1 &'
                    sh 'sleep 40' // Tiempo para que arranque
                }
            }
        }
        stage('Ejecutar DAST (ZAP)') {
            steps {
                // ZAP usará el archivo que acaba de bajar del Checkout
                sh 'zaproxy -cmd -autorun zap_scan.yaml'
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'zap_report.html', fingerprint: true
            sh 'pkill -f "node" || true' // Limpieza
        }
    }
}

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
		dir('/home/kali/juice-shop') {
	            // Usamos sudo para limpiar procesos y arrancar
	            sh 'sudo pkill -f "node" || true' 
	            // Arrancamos con sudo para evitar líos de permisos en logs
	            sh 'sudo npm start > jenkins_juice.log 2>&1 &'
	            sh 'sleep 40'
		}
	    }
	}        
	stage('Ejecutar DAST (ZAP)') {
            steps {
                // ZAP usará el archivo que acaba de bajar del Checkout
		sh 'zaproxy -cmd -autorun zap_scan.yaml'            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'zap_report.html', fingerprint: true
            sh 'pkill -f "node" || true' // Limpieza
        }
    }
}

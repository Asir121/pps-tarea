pipeline {
    agent any

    environment {
        // Forzamos a npm a no usar colores en la consola de Jenkins para limpiar logs
        NPM_CONFIG_COLOR = 'false'
    }

    stages {
        stage('Checkout & Clone Juice Shop') {
            steps {
                // Descarga tu repositorio principal (donde está el zap_scan.yaml)
                checkout scm
                
                // Limpia clones previos y descarga una copia fresca de Juice Shop
                sh 'rm -rf juice-shop'
                sh 'git clone https://github.com/juice-shop/juice-shop.git juice-shop'
            }
        }

        stage('Install & Start Juice Shop') {
            steps {
                // Importante: Asegúrate de tener Node v22 instalado en el entorno de Jenkins
                dir('juice-shop') {
                    sh 'npm install'
                    
                    echo 'Iniciando Juice Shop en segundo plano...'
                    // Arrancamos con '&' para liberarlo y redirigimos logs a un archivo temporal
                    sh 'npm start > juice_shop.log 2>&1 &'
                    
                    echo 'Esperando 60 segundos a que la aplicación esté lista...'
                    sh 'sleep 60'
                    
                    echo 'Verificando conectividad con Juice Shop...'
                    sh 'curl -I http://localhost:3000'
                }
            }
        }

        stage('Install OWASP ZAP') {
            steps {
                echo 'Instalando OWASP ZAP en el agente de Jenkins...'
                // Nota: El usuario que ejecuta Jenkins debe tener permisos de sudo sin contraseña, 
                // o bien ZAP debe estar ya preinstalado en la máquina/agente.
                sh 'sudo apt-get update && sudo apt-get install zaproxy -y'
            }
        }

        stage('Run DAST Scan') {
            steps {
                echo 'Ejecutando escaneo automatizado con el archivo de configuración...'
                // Ejecuta ZAP utilizando el archivo de automatización zap_scan.yaml de tu raíz
                sh 'zaproxy -cmd -autorun zap_scan.yaml'
            }
        }
    }

    post {
        always {
            echo 'Guardando el reporte de vulnerabilidades como artefacto...'
            // Archiva el reporte HTML generado para que puedas descargarlo desde la interfaz de Jenkins
            archiveArtifacts artifacts: 'zap_report.html', fingerprint: true, allowEmptyArchive: true
            
            echo 'Limpiando procesos de Juice Shop en segundo plano...'
            // Matamos el proceso de Node para no dejar colgado el puerto 3000 en el servidor Jenkins
            sh 'pkill -f "node" || true'
        }
    }
}

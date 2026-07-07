pipeline {
    agent any

    tools {
        nodejs "Node22"
        // Mantenemos la declaración de tus herramientas
        dockerTool 'Dockertool' 
    }

    stages {
        stage('Instalar dependencias') {
            steps {
                sh 'npm install'
                sh 'chmod -R +x ./node_modules/.bin'
            }
        }

        stage('Ejecutar tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Construir Imagen Docker') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                // Forzamos a Jenkins a usar el ejecutable del plugin 'Dockertool'
                withEnv(["PATH+DOCKER=${tool 'Dockertool'}/bin"]) {
                    sh 'docker build -t hola-mundo-node:latest .'
                }
            }
        }

        stage('Ejecutar Contenedor Node.js') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                withEnv(["PATH+DOCKER=${tool 'Dockertool'}/bin"]) {
                    sh '''
                        docker stop hola-mundo-node || true
                        docker rm hola-mundo-node || true
                        docker run -d --name hola-mundo-node -p 3000:3000 hola-mundo-node:latest
                    '''
                }
            }
        }
    }
}
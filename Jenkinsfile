pipeline {
    agent any
    tools{
        nodejs 'NodeJS'
    }
    environment{
        SONAR_PROJECT_KEY = 'threepoints'
        SONAR_SCANNER_HOME = tool 'SonarQubeScanner'
        user = 'admin'
        password = 'password'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Clonning repo...'
                git credentialsId: 'fabiopos-gh', url: 'https://github.com/fabiopos/threepoints_devops_webserver'
                echo 'Git repo cloned'
                sh 'ls'
                dir('./src') {
                    sh 'npm install'
                }
            }
        }
        stage('Test') {
            parallel {
                stage('Pruebas de SAST') {
                    steps {
                        sh 'echo "Pruebas de SAST"'
                        
                    }
                }
                stage('Imprimir env') {
                    steps {
                        sh 'pwd'
                        // Add commands to run integration tests
                    }
                }
            }
        }
        stage('SonarQube'){
            steps{
                
                withCredentials([string(credentialsId: 'node-token', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv('SonarQube') {
                        sh """
          				${SONAR_SCANNER_HOME}/bin/sonar-scanner \
          				-Dsonar.projectKey=${SONAR_PROJECT_KEY} \
            				-Dsonar.sources=. \
           				-Dsonar.host.url=http://sonarqube:9000 \
            				-Dsonar.login=${SONAR_TOKEN}
            				"""
                    }
                }
            }
        }
        stage('Configurar Archivo'){
            steps{
                script{
                    writeFile(file: 'credentials.ini', text: """
                    [credentials]
                    user=${user}
                    password=${password}""")
                    archiveArtifacts(artifacts: 'credentials.ini', followSymlinks: false)
                }
            }
        }
        stage('Build'){
            steps{
                echo 'Building image...'
                sh 'ls'
                script{
                    docker.build("devops_ws:"+"$BUILD_NUMBER")
                }
            }
        }
    }
    post{
        success { echo 'Build complete succesfully!'}
        failure { echo 'Build failed. Check logs.'}
    }
}

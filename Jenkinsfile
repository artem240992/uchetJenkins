pipeline {
    agent any

    environment {
        V8_PATH = "C:\\Program Files\\1cv8\\8.3.27.1786\\bin\\1cv8.exe"
    }

    stages {
        stage('Очистка') {
            steps {
                bat 'if exist build\\ib rmdir /s /q build\\ib'
                bat 'mkdir build\\ib'
            }
        }

        stage('Создание ИБ') {
            options { timeout(time: 5, unit: 'MINUTES') }
            steps {
                bat "\"${env.V8_PATH}\" CREATE INFOBASE /F \"%WORKSPACE%\\build\\ib\" /DisableStartupMessages /AddInList /NoTruncate /Out \"%WORKSPACE%\\create_ib.log\""
                bat 'if not exist build\\ib\\1Cv8.1CD exit /b 1'
            }
        }

        stage('Загрузка конфигурации') {
            steps {
                bat "\"${env.V8_PATH}\" DESIGNER /F \"%WORKSPACE%\\build\\ib\" /LoadConfigFromFiles \"%WORKSPACE%\\src\" /UpdateDBCfg /Out \"%WORKSPACE%\\load.log\""
            }
        }

        stage('Проверка') {
            steps {
                bat "\"${env.V8_PATH}\" DESIGNER /F \"%WORKSPACE%\\build\\ib\" /CheckConfig -ThinClient -Server -ExternalConnection /Out \"%WORKSPACE%\\check.log\""
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '*.log, build\\ib\\*.cfl', allowEmptyArchive: true
        }
        failure {
            echo 'Ошибка сборки. Логи сохранены.'
        }
    }
}
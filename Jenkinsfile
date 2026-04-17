pipeline {
    agent any

    options {
        timeout(time: 30, unit: 'MINUTES') // глобальный таймаут на всю сборку
    }

    environment {
        V8_PATH = "C:\\Program Files\\1cv8\\8.3.27.1786\\bin\\1cv8.exe"
        EMPTY_IB = "C:\\empty_ib"   // путь к пустой базе-шаблону
    }

    stages {
        stage('Очистка') {

            options {
                timeout(time: 5, unit: 'MINUTES')
            }
            steps {
                bat 'if exist build\\ib rmdir /s /q build\\ib'
                bat 'mkdir build\\ib'
            }
        }

        stage('Копирование базы-шаблона') {
            options {
                timeout(time: 5, unit: 'MINUTES')
            }
            steps {
                bat "xcopy /E /I /Y \"${env.EMPTY_IB}\" \"%WORKSPACE%\\build\\ib\""
            }
        }

        stage('Загрузка конфигурации') {
            options {
                timeout(time: 10, unit: 'MINUTES')
            }
            steps {
                bat "\"${env.V8_PATH}\" DESIGNER /F \"%WORKSPACE%\\build\\ib\" /LoadConfigFromFiles \"%WORKSPACE%\\src\" /UpdateDBCfg /Out \"%WORKSPACE%\\load.log\""
            }
        }

        stage('Проверка конфигурации') {
            options {
                timeout(time: 10, unit: 'MINUTES')
            }
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
            echo 'Сборка не удалась. Логи в архиве.'
        }
    }
}
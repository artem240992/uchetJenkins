pipeline {
    agent any

    environment {
        V8_VERSION = '8.3.27.1786'
        V8_PATH = "C:\\Program Files\\1cv8\\${V8_VERSION}\\bin\\1cv8.exe"
        // WORKSPACE будет подставлен автоматически
    }

    stages {
        stage('Подготовка') {
            steps {
                bat 'echo Current directory: %CD%'
                bat 'if exist build\\ib rmdir /s /q build\\ib'
                bat 'mkdir build\\ib'
            }
        }

        stage('Создание тестовой ИБ') {
            options {
                timeout(time: 5, unit: 'MINUTES')
            }
            steps {
                script {
                    def createCmd = "\"${env.V8_PATH}\" CREATE INFOBASE " +
                                    "/F \"%WORKSPACE%\\build\\ib\" " +
                                    "/DisableStartupMessages /AddInList /NoTruncate " +
                                    "/Out \"%WORKSPACE%\\create_ib.log\""
                    bat createCmd
                    // Проверяем, что база действительно создалась
                    bat 'if not exist build\\ib\\1Cv8.1CD exit /b 1'
                }
            }
        }

        stage('Загрузка конфигурации из файлов') {
            steps {
                // Пример загрузки выгруженной ранее конфигурации (файлы лежат в src/)
                bat "\"${env.V8_PATH}\" DESIGNER /F \"%WORKSPACE%\\build\\ib\" " +
                    "/LoadConfigFromFiles \"%WORKSPACE%\\src\" /UpdateDBCfg " +
                    "/Out \"%WORKSPACE%\\load.log\""
            }
        }

        stage('Запуск проверки 1С') {
            steps {
                // Здесь можно запустить тесты Vanessa или обычную проверку конфигурации
                bat "\"${env.V8_PATH}\" DESIGNER /F \"%WORKSPACE%\\build\\ib\" " +
                    "/CheckConfig -ThinClient -Server -ExternalConnection /Out \"%WORKSPACE%\\check.log\""
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '*.log, build\\ib\\*.cfl', allowEmptyArchive: true
            cleanWs() // опционально: удалить рабочую папку после сборки
        }
        failure {
            echo 'Сборка прервана. Смотрите логи create_ib.log, load.log, check.log'
        }
    }
}
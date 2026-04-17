pipeline {
    agent any

    options {
        timeout(time: 30, unit: 'MINUTES')
    }

    environment {
        V8_PATH = "C:\\Program Files\\1cv8\\8.3.27.1786\\bin\\1cv8.exe"
        EMPTY_IB = "C:\\empty_ib"
        VRUNNER = "C:\\Program Files\\OneScript\\bin\\vrunner.bat"
    }

    stages {
        stage('Очистка') {
            steps {
                bat 'if exist build\\ib rmdir /s /q build\\ib'
                bat 'mkdir build\\ib'
            }
        }

        stage('Копирование базы-шаблона') {
            options { timeout(time: 2, unit: 'MINUTES') }
            steps {
                bat "xcopy /E /I /Y \"${env.EMPTY_IB}\" \"%WORKSPACE%\\build\\ib\""
            }
        }

        stage('Загрузка конфигурации') {
            options { timeout(time: 10, unit: 'MINUTES') }
            steps {
                bat "\"${env.V8_PATH}\" DESIGNER /F \"%WORKSPACE%\\build\\ib\" /LoadConfigFromFiles \"%WORKSPACE%\\src\" /UpdateDBCfg /Out \"%WORKSPACE%\\load.log\""
            }
        }

        stage('Проверка наличия тестов') {
            steps {
                bat 'if not exist tools\\vanessa-automation.epf (echo ERROR: vanessa-automation.epf not found && exit /b 1)'
                bat 'if not exist features (echo ERROR: features folder not found && exit /b 1)'
            }
        }

        stage('Автотесты (Vanessa Automation)') {
    options { timeout(time: 15, unit: 'MINUTES') }
    steps {
        bat "oscript \"%WORKSPACE%\\tools\\vanessa-runner\\tools\\runner.os\" run --ibconnection \"/F%WORKSPACE%\\build\\ib\" --vanessa \"%WORKSPACE%\\tools\\vanessa-automation.epf\" --path \"%WORKSPACE%\\features\" --report-path \"%WORKSPACE%\\reports\""
            }
        }  
    }

    post {
        always {
            archiveArtifacts artifacts: '*.log, reports/**/*, build\\ib\\*.cfl', allowEmptyArchive: true
        }
        failure {
            echo 'Сборка не удалась. Логи и отчёты сохранены в архиве.'
        }
    }
}
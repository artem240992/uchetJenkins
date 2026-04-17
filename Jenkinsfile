pipeline {
    agent any

    options {
        timeout(time: 30, unit: 'MINUTES')
    }

    environment {
        V8_PATH = "C:\\Program Files\\1cv8\\8.3.27.1786\\bin\\1cv8.exe"
        EMPTY_IB = "C:\\empty_ib"   // путь к пустой базе-шаблону
    }

    stages {
        // ... (ваши существующие этапы: Очистка, Копирование базы-шаблона, Проверка наличия файлов) ...
        stage('Загрузка конфигурации') {
            options { timeout(time: 10, unit: 'MINUTES') }
            steps {
                bat "\"${env.V8_PATH}\" DESIGNER /F \"%WORKSPACE%\\build\\ib\" /LoadConfigFromFiles \"%WORKSPACE%\\src\" /UpdateDBCfg /Out \"%WORKSPACE%\\load.log\""
            }
        }

        // НОВЫЙ ЭТАП: Запуск автотестов
        stage('Автотесты (Vanessa Automation)') {
            options { timeout(time: 15, unit: 'MINUTES') } // Увеличьте, если тестов много
            steps {
                bat """
                    vanessa-runner run \
                        --ibconnection /F"%WORKSPACE%\\build\\ib" \
                        --vanessa "./tools/vanessa-automation.epf" \
                        --path "./features" \
                        --report-path "./reports"
                """
            }
        }
        // ---------------------------------------------

        // stage('Проверка конфигурации') { ... } // Этот этап можно оставить или убрать, если тесты покрывают все проверки
    }

    post {
        always {
            // Архивируем отчёты о тестах
            archiveArtifacts artifacts: '*.log, reports/*.json, reports/*.xml, build\\ib\\*.cfl', allowEmptyArchive: true
            publishHTML([ // Публикуем отчёт в Jenkins
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'reports',
                reportFiles: 'index.html',
                reportName: 'Vanessa Automation Report'
            ])
        }
        failure {
            echo 'Сборка не удалась. Проверьте логи тестов.'
        }
    }
}
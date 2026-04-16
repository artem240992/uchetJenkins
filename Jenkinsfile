pipeline {
    // Этот pipeline будет выполнен на любом доступном агенте Jenkins.
    agent any

    // --- ОПЦИИ СБОРКИ ---
    options {
        // Устанавливаем глобальный таймаут на 1 час, чтобы сборка не зависала навсегда.
        timeout(time: 1, unit: 'HOURS')
    }

    // --- ЭТАПЫ СБОРКИ ---
    stages {
        // 1. ПОДГОТОВКА
        stage('Подготовка') {
            steps {
                // Выводим текущую директорию для отладки.
                bat 'echo Current directory: %CD%'
                // Создаем каталог для временной базы, если его нет.
                bat 'if not exist build\\ib mkdir build\\ib'
                // Проверяем, существует ли ваш скрипт проверки, и останавливаем сборку, если его нет.
                bat 'if exist tools\\check.os (echo check.os found) else (echo ERROR: tools/check.os not found & exit 1)'
            }
        }

        // 2. ЗАПУСК ПРОВЕРКИ 1С
        stage('Запуск проверки 1С') {
            steps {
                // Используем директиву timeout, чтобы ограничить время выполнения самой команды 1С.
                timeout(time: 30, unit: 'MINUTES') {
                    bat '''
                        "C:\\Program Files\\1cv8\\8.3.27.1786\\bin\\1cv8.exe" DESIGNER ^
                            /F "%WORKSPACE%\\build\\ib" ^
                            /N "Тестовый" ^
                            /P "123" ^
                            /RunModeOrdinary ^
                            /DisableStartupMessages ^
                            /Out "%WORKSPACE%\\build.log" -FormatTXT ^
                            /Execute "%WORKSPACE%\\tools\\check.os"
                    '''
                }
            }
        }
    }

    // --- ДЕЙСТВИЯ ПОСЛЕ СБОРКИ ---
    post {
        // Этот блок выполняется всегда, даже если сборка упала с ошибкой.
        always {
            // Сохраняем лог 1С как артефакт сборки. Вы сможете скачать его со страницы сборки.
            archiveArtifacts artifacts: 'build.log', allowEmptyArchive: true
            // Выводим содержимое лога прямо в консоль Jenkins для быстрого анализа.
            script {
                if (fileExists('build.log')) {
                    echo '=== Содержимое build.log ==='
                    readFile('build.log').eachLine { line ->
                        echo line
                    }
                } else {
                    echo 'build.log не создан'
                }
            }
        }
    }
}
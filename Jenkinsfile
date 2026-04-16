pipeline {
    agent any
    options {
        timeout(time: 1, unit: 'HOURS')
    }
    stages {
        stage('Подготовка') {
            steps {
                bat 'echo Current directory: %CD%'
                bat 'if not exist build\\ib mkdir build\\ib'
                bat '''
                    "C:\\Program Files\\1cv8\\8.3.27.1786\\bin\\1cv8.exe" CREATE INFOBASE ^
                        /F "%WORKSPACE%\\build\\ib" ^
                        /DisableStartupMessages ^
                        /AddInList /NoTruncate
                '''
            }
        }
        stage('Загрузка конфигурации из файлов') {
            steps {
                bat '''
                    "C:\\Program Files\\1cv8\\8.3.27.1786\\bin\\1cv8.exe" DESIGNER ^
                        /F "%WORKSPACE%\\build\\ib" ^
                        /LoadConfigFromFiles "%WORKSPACE%" ^
                        /DisableStartupMessages
                '''
            }
        }
        stage('Запуск проверки 1С') {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
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
    post {
        always {
            archiveArtifacts artifacts: 'build.log', allowEmptyArchive: true
            script {
                if (fileExists('build.log')) {
                    echo '=== Содержимое build.log ==='
                    readFile('build.log').eachLine { line -> echo line }
                } else {
                    echo 'build.log не создан'
                }
            }
        }
    }
}
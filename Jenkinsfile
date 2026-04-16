pipeline {
    agent any

    options {
        timeout(time: 1, unit: 'HOURS')
    }

    stages {
        stage('Prepare') {
            steps {
                bat 'echo Current directory: %CD%'
                bat 'if not exist build\\ib mkdir build\\ib'
                bat 'if exist tools\\check.os (echo check.os found) else (echo ERROR: tools/check.os not found & exit 1)'
            }
        }
        stage('Run 1C Check') {
            steps {
                bat '''
                    "C:\\Program Files\\1cv8\\8.3.27.1786\\bin\\1cv8.exe" DESIGNER ^
                        /F "%WORKSPACE%\\build\\ib" ^
                        /N "Журавель АЮ" ^
                        /P "123" ^
                        /RunModeOrdinary ^
                        /Out "%WORKSPACE%\\build.log" -FormatTXT ^
                        /Execute "%WORKSPACE%\\tools\\check.os"
             
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'build.log', allowEmptyArchive: true
            // Сохраняем лог даже при ошибке
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
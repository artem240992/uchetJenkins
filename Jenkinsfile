// @Library('vanessa_usher_local') _

pipeline {
    agent any

    options {
        timeout(time: 30, unit: 'MINUTES')
        skipDefaultCheckout()
    }

    environment {
        V8_PATH = "C:\\Program Files\\1cv8\\8.3.27.1786\\bin\\1cv8.exe"
        EMPTY_IB = "C:\\empty_ib"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    extensions: [[$class: 'SubmoduleOption', disableSubmodules: true]],
                    userRemoteConfigs: [[url: 'https://github.com/artem240992/uchetJenkins', credentialsId: 'd3c27826-bab6-4b11-a02b-834a742b8601']]
                ])
            }
        }

        stage('Подготовка тестовой ИБ') {
            steps {
                // Создаём базу из шаблона или через команду библиотеки
                bat "xcopy /E /I /Y \"${env.EMPTY_IB}\" \"%WORKSPACE%\\build\\ib\""
            }
        }

        stage('Загрузка конфигурации') {
            steps {
                // Используем метод библиотеки для загрузки конфигурации из папки src
                loadConfigFromPath("${WORKSPACE}\\src")
            }
        }

        stage('Запуск автотестов') {
            steps {
                // Запуск тестов через библиотеку
                runVanessaTests(featuresPath: "${WORKSPACE}\\features", vanessaPath: "${WORKSPACE}\\tools\\vanessa-automation.epf")
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '*.log, reports/**/*, build\\ib\\*.cfl', allowEmptyArchive: true
        }
        failure { echo 'Сборка не удалась.' }
    }
}
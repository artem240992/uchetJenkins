pipeline {
    agent any
    stages {
        stage('Установка зависимостей') {
            steps {
                bat 'oscript -version' // проверка наличия OneScript
            }
        }
        stage('Создание базы через Vanessa') {
            steps {
                bat 'vanessa-runner create-infobase --path ./build/ib'
                bat 'vanessa-runner load-config --path ./build/ib --src ./src'
            }
        }
        stage('Проверка') {
            steps {
                bat 'vanessa-runner run-tests --path ./build/ib'
            }
        }
    }
}
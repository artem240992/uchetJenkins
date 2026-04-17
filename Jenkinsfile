@Library('jenkins-lib') _
node {
    stage('Создать базу') {
        createInfobase()
    }
    stage('Загрузить конфигурацию') {
        initFromFiles()
    }
    stage('Запустить проверки') {
        start()   // или yaxunit(), smoke() и т.д.
    }
}
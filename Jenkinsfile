// // @Library('jenkins-lib')_
// // buildPipeline('tools/pipeline/jobConfiguration.json')
// @Library('usher2') _
// buildPipeline('tools/pipeline/jobConfiguration.json')
// pipeline1C()
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/artem240992/uchetJenkins'
            }
        }
        stage('Run 1C Check') {
            steps {
                bat 'C:\\Program Files\\1cv8\\8.3.22.1750\\bin\\1cv8.exe DESIGNER /F build\\ib /N "Admin" /P "" /RunModeOrdinary /Out build.log -FormatTXT /Execute "tools\\check.os"'
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'build.log', allowEmptyArchive: true
        }
    }
}
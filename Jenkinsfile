pipeline {
    agent { docker { image 'php' } }
    stages {
        stage('build') {
            steps {
                sh 'composer install'
                sh 'php --version'
            }
        }
    }
}

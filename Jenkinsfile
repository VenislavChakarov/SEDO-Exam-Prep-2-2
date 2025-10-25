pipeline {
    agent any
    environment {
        DOTNET_ROOT = "${HOME}/.dotnet"
        PATH = "${HOME}/.dotnet:${PATH}"
    }
    triggers {
        // Poll GitHub repo every minute for changes (replace with your branch)
        pollSCM('* * * * *')
    }
    stages {
        stage('Restore') {
            steps {
                sh 'dotnet restore'
            }
        }
        stage('Build') {
            steps {
                sh 'dotnet build --configuration Release'
            }
        }
        stage('Test') {
            steps {
                sh 'dotnet test --configuration Release --no-build --verbosity normal'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}

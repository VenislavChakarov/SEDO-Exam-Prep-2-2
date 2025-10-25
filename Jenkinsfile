pipeline {
    agent any

    stages {
        stage('Restore .NET packages') {
            steps {
                echo 'Restoring .NET packages...'
                sh 'dotnet restore'
            }
        }

        stage('Build .NET project') {
            steps {
                echo 'Running build...'
                sh 'dotnet build --no-restore'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'dotnet test --no-build --verbosity normal'
            }
        }
    }
}
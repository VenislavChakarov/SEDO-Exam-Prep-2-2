pipeline {
    agent any

    stages {
        stage('Install .NET 6') {
            steps {
                sh '''
                    wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh
                    bash dotnet-install.sh --version 6.0.426 --install-dir $HOME/dotnet
                    export DOTNET_ROOT=$HOME/dotnet
                    export PATH=$DOTNET_ROOT:$PATH
                    dotnet --version
                '''
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build'
            }
        }

        stage('Test') {
            steps {
                sh 'dotnet test --no-build --verbosity normal'
            }
        }
    }
}

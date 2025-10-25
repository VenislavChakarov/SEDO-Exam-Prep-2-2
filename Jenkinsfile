pipeline {
    agent any

    stages {
        stage('Install .NET 6') {
            steps {
                sh '''
                    # Use curl instead of wget
                    curl -sSL https://dot.net/v1/dotnet-install.sh -o dotnet-install.sh
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

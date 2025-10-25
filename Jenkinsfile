pipeline {
    agent any
    environment {
        DOTNET_ROOT = "${HOME}/.dotnet"
        PATH = "${HOME}/.dotnet:${PATH}"
    }
    stages {
        stage('Install .NET 6 SDK') {
            steps {
                sh '''
                echo "Installing .NET 6 SDK if not present..."
                mkdir -p $HOME/.dotnet
                curl -sSL https://dot.net/v1/dotnet-install.sh -o dotnet-install.sh
                bash dotnet-install.sh --channel 6.0 --install-dir $HOME/.dotnet
                export PATH=$HOME/.dotnet:$PATH
                dotnet --info
                '''
            }
        }

        stage('Restore') {
            steps {
                sh 'export PATH=$HOME/.dotnet:$PATH && dotnet restore'
            }
        }

        stage('Build') {
            steps {
                sh 'export PATH=$HOME/.dotnet:$PATH && dotnet build --configuration Release'
            }
        }

        stage('Test') {
            steps {
                sh 'export PATH=$HOME/.dotnet:$PATH && dotnet test --no-build --verbosity normal'
            }
        }
    }
}

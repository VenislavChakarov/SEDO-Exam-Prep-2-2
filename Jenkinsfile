pipeline {
    agent any

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

                stage('Setup .NET') {
                        steps {
                                // Install .NET SDK 6.0 using the cross-platform dotnet-install script.
                                // This uses curl if available, falls back to wget, and installs into $HOME/.dotnet
                                sh '''
                                        set -euo pipefail
                                        DOTNET_DIR="$HOME/.dotnet"
                                        DOTNET_BIN="$DOTNET_DIR/dotnet"

                                        if [ -x "$DOTNET_BIN" ]; then
                                            echo ".NET already present at $DOTNET_BIN"
                                            exit 0
                                        fi

                                        if command -v curl >/dev/null 2>&1; then
                                            curl -sSL https://dot.net/v1/dotnet-install.sh -o dotnet-install.sh
                                        elif command -v wget >/dev/null 2>&1; then
                                            wget -q https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh
                                        else
                                            echo "Neither curl nor wget found on the agent. Please install one or preinstall .NET SDK on the agent." >&2
                                            exit 1
                                        fi

                                        chmod +x dotnet-install.sh
                                        ./dotnet-install.sh --channel 6.0 --install-dir "$DOTNET_DIR"
                                        rm -f dotnet-install.sh
                                        echo "Installed dotnet to $DOTNET_DIR"
                                '''
                        }
                }

        stage('Restore Dependencies') {
            steps {
                sh '$HOME/.dotnet/dotnet restore'
            }
        }

        stage('Build') {
            steps {
                sh '$HOME/.dotnet/dotnet build --no-restore'
            }
        }

        stage('Test') {
            steps {
                sh '$HOME/.dotnet/dotnet test --no-build --verbosity normal'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

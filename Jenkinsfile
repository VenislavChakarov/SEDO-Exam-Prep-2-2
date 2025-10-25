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
                                if command -v curl >/dev/null 2>&1; then
                                    curl -sSL https://dot.net/v1/dotnet-install.sh -o dotnet-install.sh
                                elif command -v wget >/dev/null 2>&1; then
                                    wget -q https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh
                                else
                                    echo "Neither curl nor wget found on the agent. Please install one or preinstall .NET SDK on the agent." >&2
                                    exit 1
                                fi
                                bash dotnet-install.sh --channel 6.0 --install-dir $HOME/.dotnet
                                rm -f dotnet-install.sh
                                export PATH=$HOME/.dotnet:$PATH
                                dotnet --info
                '''
            }
        }

                stage('Prepare workspace (copy to no-space path)') {
                        steps {
                                script {
                                        // Create a temporary dir without spaces and copy the repo there to avoid VSTest issues with spaces in paths
                                        env.WORKDIR = sh(script: 'mktemp -d /tmp/jenkins_build_XXXXXX', returnStdout: true).trim()
                                }
                                sh '''
                                echo "Copying workspace to $WORKDIR"
                                # Use rsync for accurate copying; exclude .git to keep it small
                                rsync -a --exclude .git ./ "$WORKDIR/"
                                ls -la "$WORKDIR"
                                '''
                        }
                }

        stage('Restore') {
            steps {
                dir("${env.WORKDIR}") {
                    sh 'export PATH=$HOME/.dotnet:$PATH && $HOME/.dotnet/dotnet restore'
                }
            }
        }

        stage('Build') {
            steps {
                dir("${env.WORKDIR}") {
                    sh 'export PATH=$HOME/.dotnet:$PATH && $HOME/.dotnet/dotnet build --configuration Release'
                }
            }
        }

        stage('Test') {
            steps {
                dir("${env.WORKDIR}") {
                    // Run tests from the copied workspace. Use --no-build because we already built Release above.
                    // Specify configuration Release to match the build output.
                    sh 'export PATH=$HOME/.dotnet:$PATH && $HOME/.dotnet/dotnet test --no-build --configuration Release --verbosity normal'
                }
            }
        }
    }
    post {
        always {
            // Clean up the temporary workspace if it was created
            sh 'if [ -n "${WORKDIR:-}" ] && [ -d "${WORKDIR}" ]; then rm -rf "${WORKDIR}"; fi'
            cleanWs()
        }
    }
}

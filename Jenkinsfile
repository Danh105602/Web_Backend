pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo '=== CHECKOUT SOURCE CODE ==='
                checkout scm
            }
        }

        stage('Restore') {
            steps {
                echo '=== DOTNET RESTORE ==='
                bat '''
                    dotnet tool restore
                    cd Server
                    dotnet restore ShoesStoreApp.Server.sln
                '''
            }
        }

        stage('Build') {
            steps {
                echo '=== DOTNET BUILD ==='
                bat '''
                    cd Server
                    dotnet build ShoesStoreApp.Server.sln --configuration Release --no-restore
                '''
            }
        }

        stage('Database Migration') {
            steps {
                echo '=== DATABASE MIGRATION ==='
                bat '''
                    dotnet tool restore
                    dotnet tool run dotnet-ef database update ^
                        --project .\\Server\\ShoesStoreApp.DAL ^
                        --startup-project .\\Server\\ShoesStoreApp.PLA
                '''
            }
        }

        stage('Publish') {
            steps {
                echo '=== DOTNET PUBLISH ==='
                bat '''
                    cd Server
                    dotnet publish .\\ShoesStoreApp.PLA\\ShoesStoreApp.PLA.csproj ^
                        --configuration Release ^
                        --output ..\\publish
                '''
            }
        }
    }

    post {
        success {
            echo '=== CI/CD SUCCESS ==='
            echo 'Backend published successfully.'

            archiveArtifacts artifacts: 'publish/**',
                             fingerprint: true,
                             allowEmptyArchive: false
        }

        failure {
            echo '=== CI/CD FAILED ==='
        }
    }
}
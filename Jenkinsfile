pipeline {
    agent any 
    stages {
        stage ('Build Backend') {
            steps {
                bat 'dotnet clean && dotnet build'
            }
        }
        stage ('Unit Tests') {
            steps {
                bat 'dotnet test --logger:"trx;logFileName=%WORKSPACE%/tests/report.xml"'
            }
        }
         stage ('Sonar Analysis') {
            environment{
                scannerHome = tool 'SONAR_SCANNER'
            } 
            steps {
                withSonarQubeEnv('SONAR_LOCAL')
                bat 'SonarScanner.MSBuild.exe begin /k:"TesteIntegracaoxUnit"'
            }
        }
    }
}



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
                def sqScannerMsBuildHome = tool 'Scanner for MSBuild 5.9'
            } 
            steps {
                 bat "${sqScannerMsBuildHome}\\SonarQube.Scanner.MSBuild.exe begin /k:TesteIntegracaoxUnit"
                 bat 'MSBuild.exe /t:Rebuild'
                 bat "${sqScannerMsBuildHome}\\SonarQube.Scanner.MSBuild.exe end"
            }
        }
    }
}



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
                xunit (tools: [ MSTest(pattern: 'tests/*.xml') ], skipPublishingChecks: false)
            }
        }
        stage ('Sonar Analysis') {
            environment{
                scannerHome = tool 'SONAR_SCANNER'
            } 
            steps {
                 bat 'dotnet C:\\Users\\Metris\\.jenkins\\tools\\hudson.plugins.sonar.MsBuildSQRunnerInstallation\\SONAR_MSBuild\\SonarScanner.MSBuild.dll begin /k:TesteIntegracaoxUnit /n: /v: /d:sonar.host.url=http://localhost:9000/'
                 bat 'dotnet build'
                 bat 'dotnet C:\\Users\\Metris\\.jenkins\\tools\\hudson.plugins.sonar.MsBuildSQRunnerInstallation\\SONAR_MSBuild\\SonarScanner.MSBuild.dll end'
            }
        }
    }
}



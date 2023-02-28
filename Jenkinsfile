pipeline {
    agent any 
    stages {
        stage ('Build Backend') {
            steps {
                bat 'dotnet C:\\Users\\Metris\\.jenkins\\tools\\hudson.plugins.sonar.MsBuildSQRunnerInstallation\\SONAR_MSBuild\\SonarScanner.MSBuild.dll begin /k:TesteIntegracaoxUnit /n: /v: /d:sonar.host.url=http://localhost:9000/'
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
            steps {
                withSonarQubeEnv('SONAR_LOCAL'){
                 bat 'dotnet C:\\Users\\Metris\\.jenkins\\tools\\hudson.plugins.sonar.MsBuildSQRunnerInstallation\\SONAR_MSBuild\\SonarScanner.MSBuild.dll end'
                }
            }
        }
          stage ('Quality Gate') {
            steps {
                sleep(15)
                timeout(time: 1, unit: 'MINUTES'){
                 waitForQualityGate abortPipeline: true
                 }
            }
        }
    }
}



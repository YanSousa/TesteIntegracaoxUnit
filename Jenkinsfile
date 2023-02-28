pipeline {
    agent any 
    stages {
        stage ('Build Backend') {
            steps {
                bat 'dotnet C:\\Users\\Metris\\.jenkins\\tools\\sonar-scanner-msbuild-5.11.0.60783-net5.0\\SonarScanner.MSBuild.dll begin /k:TesteIntegracaoxUnit /n: /v: /d:sonar.host.url=http://localhost:9000/'
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
                 bat 'dotnet C:\\Users\\Metris\\.jenkins\\tools\\sonar-scanner-msbuild-5.11.0.60783-net5.0\\SonarScanner.MSBuild.dll end'
                }
            }
        }
        stage ('Quality Gate') {
            steps {
                sleep(30)
                timeout(time: 5, unit: 'MINUTES'){
                 waitForQualityGate abortPipeline: true
                 }
            }
        }
        stage ('Vulnerability') {
            steps {
               powershel 'del .\\report_2023.R3.txt' 
               powershell 'dotnet list package --vulnerable --include-transitive >>report_2023.R3.txt'
               findFiles excludes: 'report_2023.R3.txt'
               echo "File created successfully!"
            }
        }
        stage ('ISO') {
            steps {
               archiveArtifacts artifacts: '**/report_2023.R3.txt, **/*.iso',
                                           allowEmptyArchive: true,
                                           fingerprint: true,
                                           onlyIfSuccessful: true
            }
        }
    }
}



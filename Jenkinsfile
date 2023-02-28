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
               bat 'dotnet restore "%WORKSPACE%.jenkins\\workspace\\Pepiline\\TesteIntegracaoxUnit.sln" --configfile "%WORKSPACE%.jenkins\\workspace\\Pepiline.nuget\\nuget.config"'
               bat 'dotnet list "%WORKSPACE%.jenkins\\workspace\\Pepiline\\TesteIntegracaoxUnit.sln" package --vulnerable --include-transitive >> "%WORKSPACE%\\report_2023.R3.txt"'
               echo done >> "%WORKSPACE%\\report_2022.R1.txt"
               bat 'git reset --hard'
            }
        }
          stage ('ISO') {
            steps {
               archiveArtifacts artifacts: '**/*.txt',
                                           allowEmptyArchive: true,
                                           fingerprint: true,
                                           onlyIfSuccessful: true
            }
        }
    }
}



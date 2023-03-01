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
            environment{
                MY_BUILD_NUMBER = "${CUSTOM_BUILD_NUMBER}"
            }
            steps {
               bat "del VulnerabilityReport_*.txt"
               powershell "dotnet list package --vulnerable --include-transitive >>VulnerabilityReport_${BUILD_NUMBER}.txt"
               findFiles excludes: "VulnerabilityReport_${BUILD_NUMBER}.txt"
               echo "File VulnerabilityReport_${BUILD_NUMBER} created successfully!"
            }
        }
        stage ('ISO') {
            steps {
               archiveArtifacts artifacts: "**/VulnerabilityReport_*.txt, **/*.iso",
                                           allowEmptyArchive: true,
                                           fingerprint: true,
                                           onlyIfSuccessful: true
            }
        }
        stage ('Health Check Server') {
            steps {
               script {
                def server = "10.61.249.201"
                def response = bat(script: "ping -n 1 ${server}", returnStdout: true)
                if (response.contains("Reply")){
                 echo "The server ${server} is online"
                } else {
                  error "The server ${server} is offline"
                }
               }
            }
        }
    }
}



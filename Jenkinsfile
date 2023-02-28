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
                when { expression { return pipelineParams.QualityGate } }
                steps {
                    script {
                        def maxRetry = 2
                        def i = 0
                        for (i=0; i<maxRetry; i++){
                            try {
                                timeout(time: 5, unit: 'SECONDS') {
                                    def qualitygate = waitForQualityGate()
                                    if (qualitygate.status != "OK") {
                                        echo "Pipeline aborted due to quality gate coverage failure: ${qualitygate.status}"
                                    }
                                    else {
                                        echo "SonarQube analysis succesfull"
                                    }
                                }
                            }
                            catch(Exception e) {
                                if (i == maxRetry-1) {
                                    println "No of Tries : ${i}, ${e}"
                                    throw e        
                                }
                            }
                        }                            
                    }
                }
            }
    }
}



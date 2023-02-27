pipeline {
    agent any 
    stages {
        stage ('Build Backend') {
            steps {
                bat 'dotnet clean && dotnet build'
            }
        }
    }
}

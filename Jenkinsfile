pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }
    parameters{
        booleanParam(name: 'deploy', defaultValue: false, description: 'Toggle this value')
    }
    environment {
        // Initialize appVersion as an empty string
        appVersion = ''
        nexusUrl='nexus.vigneshdev.online:8081'

    }
    stages {
        stage('Read the Version') {
            steps {
                script {
                    // Read the JSON file and extract the version
                    def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version // Fixed typo from 'versioin' to 'version'
                    echo "Application version: ${appVersion}" // Use Groovy string interpolation
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh """
                npm install
                ls -ltr
                echo "Application version: ${appVersion}" // Use Groovy string interpolation
                """
            }
        }

        stage('Build') { // Fixed typo from 'BUID' to 'Build'
            steps {
                sh """
                zip -q -r backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip
                ls -ltr
                """
            }
        }
        stage('Nexus Artifact Upload'){
            steps{
                script{
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${appVersion}",
                        repository: "backend",
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [artifactId: "backend" ,
                            classifier: '',
                            file: "backend-" + "${appVersion}" + '.zip',
                            type: 'zip']
                        ]
                    )
                }
            }
        } 
        stage('Deploy'){
            when{
                expression{
                    params.deploy
                }
            }
            steps{
                script{
                    def params = [
                        string(name: 'appVersion', value: "${appVersion}")
                    ]
                    build job: 'backend-deploy', parameters: params, wait: false
                }
            }
        } 

    }
    post {
        always {
            echo "I always run"
            deleteDir() // Fixed method call from 'delete dir)()' to 'deleteDir()'
        }
    }
}

pipeline {
    agent any
    parameters {
        choice (
            choices: ['MAVEN', 'GRADLE'],
            description: 'Please choose a build tool:',
            name: 'BUILD_TOOL')
    }
    stages {
        stage('Build') {
            steps {
                build job: 'release', parameters: [string(name: 'BUILD_TOOL', value: params.BUILD_TOOL)]
            }
        }
        stage('Deploy on test') {
            steps {
                build job: 'deploy'
            }
        }
        stage("Check deployment on test") {
            steps {
                script {
                    final String url = "http://localhost:8081/web/hello.jsp"
                    final def (String request, String response, String code) =
                            bat(script: "curl -s -w \"\\n%%{response_code}\"  $url", returnStdout: true)
                                .trim()
                                .tokenize("\r\n")
                    env.http_code = code
                }
            }
        }
        stage('Deploy on production') {
            when {
                expression { env.http_code == '200' }
            }
            steps {
                build job: 'deploy_cloud'
            }
        }
    }
}
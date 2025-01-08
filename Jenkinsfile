pipeline {
    agent {
        label 'agent-1'
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        retry(1)
    }
    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'qa', 'uat', 'pre-prod', 'prod'], description: 'Select your environment')
        string(name: 'version', description: 'Enter your application version')
    }
    environment {
        appVersion = '' // this will become global, we can use across pipeline
        region = "us-east-1"
        account_id = "816069152585"
        project = "expense"
        environment = ''
        component = "backend"
    }
    stages {
        stage('Setup Environment'){
            steps {
                script {
                    environment = params.ENVIRONMENT
                    appVersion = params.version
                }
            }
        }
        stage('Deploy'){
            steps{
                steps {
                    withAWS(region: 'us-east-1', credentials: 'aws-creds') {
                        sh """
                            aws eks update-kubeconfig --region ${region} --name ${project}-${environment}
                            cd helm
                            sed -i 's/IMAGE_VERSION/${appVersion}/g' values-${environment}.yaml
                            helm upgrade --install ${component} -n ${project} -f values-${environment}.yaml .
                        """
                    }
                }
            }
        }
    }

    post {
        always{
            echo "This section runs always"
            deleteDir()
        }
        success{
            echo "This section runs when pipeline success"
        }
        failure{
            echo "This section runs when pipeline failure"
        }
    }
}
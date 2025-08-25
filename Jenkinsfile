pipeline {
    agent  {
        label 'AGENT-1'
    }
    environment { 
            appVersion= ''
            REGION = "us-east-1"
            ACC_ID = "990063103412"
            PROJECT = "roboshop"
            COMPONENT = "catalogue" 
    }
    options {
        timeout(time: 30, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }
     parameters {
        string(name: 'appVersion',  description: 'image version of the applicatio ?')
        choice(name: 'deploy_to', choices: ['dev', 'qa', 'prod'], description: 'pick the Environment')

    } 
    // Build
    stages {
        stage('Deploy') {
            steps {
                 script {
                    withAWS(credentials: 'aws-auth', region: 'us-east-1') {
                        sh """
                         aws eks update-kubeconfig --region $REGION --name "$PROJECT-${params.deploy_to}"
                            kubectl get nodes
                            kubectl apply -f 01-namespace.yaml
                            sed -i "s/IMAGE_VERSION/${params.appVersion}/g" values-${params.deploy_to}.yaml
                            helm upgrade --install $COMPONENT -f values-${params.deploy_to}.yaml -n $PROJECT .
                        """
                    }
                }
            }
        }
    }

    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'Hello Success'
        }
        failure { 
            echo 'Hello Failure'
        }
    }
}  
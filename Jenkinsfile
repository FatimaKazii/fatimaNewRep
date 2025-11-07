pipeline {
    agent any
    
    stages {        
        
        stage('Test') {
            steps {
                sh 'terraform init'
                echo 'Terraform init completed successfully!'
                script {
                    echo 'Starting formatting check...'
                    def formatCheck = sh(script: 'terraform fmt -check', returnStatus: true)
                    if (formatCheck == 0) {
                        echo 'Terraform formatting is correct!'
                    } else {
                        echo 'Terraform formatting issues found. Fixing...'
                        sh 'terraform fmt'
                        echo 'Terraform formatting fixed!'
                    }
                }
                
                script {
                    echo 'Starting terraform validation...'
                    def validateCheck = sh(script: 'terraform validate', returnStatus: true)
                    if (validateCheck == 0) {
                        echo 'Terraform validation completed successfully!'
                    } else {
                        echo 'Terraform validation failed!'
                        error('Validation failed - stopping pipeline')
                    }
                }
            }
        }
        
        stage('Build') {
            steps {
                
                sh 'terraform plan'
                echo 'Terraform plan completed successfully!'
            }
        }
        
         stage('Manual Approval') {
            steps {
                input message: 'Do you want to deploy the infrastructure?', ok: 'Deploy'
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying infrastructure...'
                sh 'terraform apply -auto-approve'
                echo 'Deployment completed successfully!'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
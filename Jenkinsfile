pipeline {
    agent any

    options {
        skipDefaultCheckout()
    }

    parameters {
        booleanParam(name: 'VALIDATE_TERRAFORM', defaultValue: false, description: 'Check to validate Terraform configurations for syntax errors and consistency')
        booleanParam(name: 'PLAN_TERRAFORM', defaultValue: false, description: 'Check to plan Terraform changes and preview the infrastructure modifications')
        booleanParam(name: 'APPLY_TERRAFORM', defaultValue: false, description: 'Check to apply Terraform changes and provision the EKS cluster on AWS')
        booleanParam(name: 'DESTROY_TERRAFORM', defaultValue: false, description: 'Check to destroy the Terraform-managed EKS cluster and related resources')
    }

    stages {
        stage('Checkout') {
            steps {
                deleteDir()

                git branch: 'main',
                    url: 'https://github.com/Manikandannew/EKS_Cluster_Terraform.git'

                sh 'ls -lart'
            }
        }

        stage('Terraform Init') {
            steps {
                retry(3) {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-crendentails-vgs']]) {
                        sh 'echo "=================Terraform Init=================="'
                        sh 'sleep 5 && terraform init -input=false'
                    }
                }
            }
        }

        stage('Terraform Validate') {
            when {
                expression { params.VALIDATE_TERRAFORM }
            }
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-crendentails-vgs']]) {
                    sh 'echo "=================Terraform Validate=================="'
                    sh 'terraform validate'
                }
            }
        }

        stage('Terraform Plan') {
            when {
                expression { params.PLAN_TERRAFORM }
            }
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-crendentails-vgs']]) {
                    sh 'echo "=================Terraform Plan=================="'
                    sh 'terraform plan'
                }
            }
        }

        stage('Terraform Apply') {
            when {
                expression { params.APPLY_TERRAFORM }
            }
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-crendentails-vgs']]) {
                    sh 'echo "=================Terraform Apply=================="'
                    sh 'terraform apply -auto-approve'
                    sh 'echo "Saving Terraform output to output.txt"'
                    sh 'terraform output > output.txt'
                    sh 'ls -lart'
                }
            }
        }

        stage('Terraform Destroy') {
            when {
                expression { params.DESTROY_TERRAFORM }
            }
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-crendentails-vgs']]) {
                    sh 'echo "=================Terraform Destroy=================="'
                    sh 'terraform destroy -auto-approve'
                }
                sh 'echo "===== Before Cleaning Workspace ====="'
                sh 'ls -lart'
                cleanWs()
                sh 'echo "===== After Cleaning Workspace ====="'
                sh 'ls -lart'
            }
        }
    }

    post {
        success {
            echo 'Terraform executed successfully!'
        }
        failure {
            echo 'Terraform execution failed!'
        }
    }
}


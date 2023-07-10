pipeline {
    agent any

    parameters {
        string(name: 'AWS_ACCESS_KEY_ID', defaultValue: '', description: 'AWS Access Key ID')
        string(name: 'AWS_SECRET_ACCESS_KEY', defaultValue: '', description: 'AWS Secret Access Key')
        string(name: 'AWS_DEFAULT_REGION', defaultValue: 'eu-west-1', description: 'AWS Default Region')
    }

    environment {
        AWS_ACCESS_KEY_ID = "${params.AWS_ACCESS_KEY_ID}"
        AWS_SECRET_ACCESS_KEY = "${params.AWS_SECRET_ACCESS_KEY}"
        AWS_DEFAULT_REGION = "${params.AWS_DEFAULT_REGION}"
    }

    stages {
        stage('Provision EKS Cluster') {
            steps {
                script {
                    def terraformFolder = 'k8s-eks-with-terraform'

                    // Clone repository if it doesn't exist
                    if (!fileExists(terraformFolder)) {
                        git 'git@github.com:warolv/k8s-guides.git'
                    }

                    // Change to the Terraform code directory
                    dir(terraformFolder) {
                        sh 'terraform init'
                        sh 'terraform plan'
                        sh 'terraform apply -auto-approve'
                    }
                }
            }
        }

        stage('Configure Kubectl') {
            steps {
                sh 'aws eks update-kubeconfig --name k8s-guides'
            }
        }

        stage('Deploy Nginx') {
            steps {
                sh 'kubectl run nginx-test --image=nginx'
            }
        }
    }
}

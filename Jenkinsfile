@Library('jenkins-shared-library@main') _
pipeline {
    agent {
        kubernetes {
            yaml '''
                apiVersion: v1
                kind: Pod
                spec:
                  containers:
                  - name: terraform
                    image: techiescamp/terraform-agent:2.0.0
                    command:
                    - cat
                    tty: true
            '''
        }
    }

    parameters {
        choice(name: 'ACTION', choices: ['apply', 'destroy'], description: 'Select whether to apply or destroy infrastructure.')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Tflint') {
            steps {
                container('terraform') {
                    script {
                        sh '''
                            tflint --chdir=ec2
                            '''
                    }
                }
            }
        }
        stage('Terraform Init') {
            steps {
                container('terraform') {
                    script {
                        sh '''
                            terraform -chdir=ec2 init
                            '''
                    }
                }
            }
        }
        stage('Checkov') {
            steps {
                container('terraform') {
                    script {
                        sh '''
                            checkov --directory ecr
                            '''
                    }
                }
            }
        }
        stage('Terraform Plan') {
            steps {
                container('terraform') {
                    script {
                        sh '''
                            terraform -chdir=ec2 plan
                            '''
                    }
                }
            }
        }
        stage('Terraform Apply') {
            steps {
                container('terraform') {
                    script {
                        sh '''
                            terraform -chdir=ec2 apply
                            '''
                    }
                }
            }
        }
    }
}

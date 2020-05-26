pipeline {
    agent any
    environment {
        NAME_OF_CLUSTER = 'capstone'
    }
    stages {
        stage('Authenticate into AWS') {
            steps {
                withAWS(region:'eu-central-1', credentials:'AWS-EKS') {
                    sh 'aws s3 ls'
                }
            }
        }
        stage('Create the EKS-k8s cluster') {
            steps {
                withAWS(region:'eu-central-1', credentials:'AWS-EKS') {
                    withCredentials([file(credentialsId: 'irtaza06-public-key', variable: 'PUBKEY')]) {
                        sh """
                        eksctl create cluster \
                            --name ${NAME_OF_CLUSTER} \
                            --version 1.16 \
                            --region eu-central-1 \
                            --nodegroup-name worker-nodes \
                            --node-type t2.micro \
                            --nodes 2 \
                            --nodes-min 2 \
                            --nodes-max 2 \
                            --ssh-access \
                            --ssh-public-key   ${PUBKEY} \
                            --managed
                        """
                    }
                        
                }

            }
        }
        stage('Create configfile/Update context') {
            steps {
                withAWS(region:'eu-central-1', credentials:'AWS-EKS') {
                    sh 'aws eks update-kubeconfig --name ${NAME_OF_CLUSTER} '
                }
            }
        }
    }
}


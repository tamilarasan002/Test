pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = "ipsps-tnd.kfon.ksitil.org:5000"
        KUBE_CONFIG = credentials('admin_conf')
        REMOTE_SERVER = "root@172.20.40.27"
        REMOTE_PATH = "/home/ipmed"

    }

    stages {
        stage('SSH to Remote Server') {
            steps {
                sshagent(['sshinfo']) {
                    sh "scp -o StrictHostKeyChecking=no /home/esakkim/nginx.tar ${REMOTE_SERVER}:${REMOTE_PATH}/nginx.tar"
                    sh "ssh ${REMOTE_SERVER} docker load -i ${REMOTE_PATH}/nginx.tar"
                }
                echo "Success: Loaded Docker image from tar file"
            }
        }


        stage('Pushing docker image') {
            steps {
                script {
                    sshagent(['sshinfo']) {
                        sh "ssh ${REMOTE_SERVER} docker push ${DOCKER_REGISTRY}/nginx:v5"
                    }
                }
            }
        }

        stage('Deploy App to Kubernetes') {
            steps {
                script {
                    sshagent(['sshinfo']) {
                        withEnv(["KUBECONFIG=${KUBE_CONFIG}"]) {
                            sh "kubectl apply -f /home/esakkim/nginx.yaml"
                        }
                    }
                }
            }
        }
    }
}

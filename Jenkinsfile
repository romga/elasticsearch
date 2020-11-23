def prepareK8sTools(){
    sh '''#!/bin/bash -e
        mkdir .bin && cd .bin \
        && curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.16.13/bin/linux/amd64/kubectl \
        && chmod +x kubectl \
        && curl -LO https://get.helm.sh/helm-v3.4.0-rc.1-linux-amd64.tar.gz \
        && curl -LO https://github.com/roboll/helmfile/releases/download/v${HELMFILE_VERSION}/helmfile_linux_amd64 \
        && mv helmfile_linux_amd64 helmfile \
        && chmod +x helmfile \
        && curl -SsL https://get.helm.sh/helm-v${HELM3_VERSION}-linux-amd64.tar.gz | tar -xzf - \
        && mv linux-amd64/helm . \
        && chmod +x helm \
        && rm -rf linux-amd64/ \
        && pip install awscli \
'''
}

pipeline{
    agent any
    environment {
        HELM3_VERSION = '3.3.4'
        HELMFILE_VERSION = '0.132.0'

    }
    stages {
        stage('prepare') {
            steps {
                prepareK8sTools()
            }
        }
        stage('deploy') {
            steps {
                withAWS(credentials: "aws", region: "eu-west-1") {
                    sh '''#!/bin/bash -e
                    export PATH=.bin:/home/jenkins/.local/bin:$PATH
                    aws eks update-kubeconfig --name sndbx  --alias sndbx
                    helmfile sync
                    '''
                }
            }
        }
    }
}


pipeline {
    agent { label 'registry-agent' }

    triggers {
        pollSCM('H/2 * * * *')
    }

    environment {
        REGISTRY = 'registry.assign.eabdosh.300.ops:5000'
        IMAGE    = "${REGISTRY}/bind-secondary"
        TAG      = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Build') {
            steps {
                sh 'podman build -t "$IMAGE:$TAG" .'
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'registry-creds',
                                                  usernameVariable: 'REG_USER',
                                                  passwordVariable: 'REG_PASS')]) {
                    sh '''
                        echo "$REG_PASS" | podman login "$REGISTRY" -u "$REG_USER" --password-stdin
                        podman push "$IMAGE:$TAG"
                        podman logout "$REGISTRY"
                    '''
                }
            }
        }
    }
}

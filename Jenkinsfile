pipeline {
    agent any

    environment {
        DOCKERIMAGE = "touchworldtech/twt-touchtraks-inhouse-ui"
        TAGNAME = "latest"
        CLUSTERNAME = "twt-ap-south-2-prd-eks"
        REGION = "ap-south-2"
        DEPLOYMENT = "touchtracks-ui-prod"
        NAMESPACE = "touchtracks-prod"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: '*/release/prod-release',
                url: 'https://nixontwt@bitbucket.org/nixontwt/twt-touchtraks-inhouse-ui.git',
                credentialsId: 'bitbucket-creds'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install --legacy-peer-deps'
            }
        }

        stage('Build Application') {
            steps {
                sh 'npm run build -- --configuration production'
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    TIMESTAMP = sh(script: "date +'%d%m%Y-%H%M%S'", returnStdout: true).trim()

                    sh """
                    docker build -t ${DOCKERIMAGE}:${TAGNAME} -t ${DOCKERIMAGE}:${TIMESTAMP} .
                    docker push ${DOCKERIMAGE}:${TAGNAME}
                    docker push ${DOCKERIMAGE}:${TIMESTAMP}
                    """
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh """
                aws eks update-kubeconfig --name ${CLUSTERNAME} --region ${REGION}
                kubectl rollout restart deploy ${DEPLOYMENT} -n ${NAMESPACE}
                """
            }
        }
    }

    post {
        success {
            echo "Deployment Successful 🚀"
        }
        failure {
            echo "Pipeline Failed ❌"
        }
    }
}
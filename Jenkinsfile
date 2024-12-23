pipeline {
    agent any
    environment {
        IMAGE_NAME = 'gautam1998/jenkins-flask-app'
        IMAGE_TAG = "${IMAGE_NAME}:${env.BUILD_NUMBER}"
        // KUBECONFIG = credentials('kubeconfig-credentials-id')

    }
    stages {
        stage('Install Python') {
            steps {
                sh '''
                # Define Python version and download URL
                PYTHON_VERSION=3.10.9
                PYTHON_TAR=https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tgz

                # Download and extract Python
                curl -O $PYTHON_TAR
                tar -xzf Python-$PYTHON_VERSION.tgz

                # Build and install Python
                cd Python-$PYTHON_VERSION
                ./configure --enable-optimizations
                make
                sudo make install

                # Verify installation
                python3 --version
                pip3 --version
                '''
            }
        }
        stage('Setup') {
            steps {
                sh "pip install -r requirements.txt"
            }
        }
        stage('Test') {
            steps {
                sh "pytest"
                sh "whoami"
            }
        }
        stage('Login to docker hub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                sh 'echo ${dockerhubpwd} | docker login -u gautam1998 --password-stdin'}
                echo 'Login successfully'
            }
        }
        stage('Build Docker Image')
        {
            steps
            {
                sh 'docker build -t ${IMAGE_TAG} .'
                echo "Docker image build successfully"
                sh "docker images"
            }
        }
        stage('Push Docker Image')
        {
            steps
            {
                sh 'docker push ${IMAGE_TAG}'
                echo "Docker image push successfully"
            }
        }
        // stage('Deploy to EKS Cluster') {
        //     steps {
        //         sh "kubectl apply -f deployment.yaml"
        //         echo "Deployed to EKS Cluster"
        //     }
        // }
    }
}

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
                set -e  # Exit immediately if a command exits with a non-zero status
        
                # Define Python version and download URL
                PYTHON_VERSION=3.10.9
                PYTHON_TAR_URL=https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tgz
                PYTHON_TAR_FILE=Python-$PYTHON_VERSION.tgz
        
                echo "Downloading Python $PYTHON_VERSION..."
                curl -O $PYTHON_TAR_URL
        
                echo "Extracting Python tarball..."
                tar -xzf $PYTHON_TAR_FILE
        
                echo "Building and installing Python..."
                cd Python-$PYTHON_VERSION
                ./configure --enable-optimizations
                make -j$(nproc)  # Use all available CPU cores for faster build
                sudo make install
        
                echo "Cleaning up temporary files..."
                cd ..
                rm -rf Python-$PYTHON_VERSION $PYTHON_TAR_FILE
        
                echo "Verifying Python installation..."
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

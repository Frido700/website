pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "hshar/webapp:${env.BUILD_NUMBER}"
        TEST_SERVER = "ubuntu@172.31.88.142"
        PROD_SERVER = "ubuntu@172.31.87.84"
        SSH_KEY = credentials('myssh_key') 
    }
    
    stages {
        stage('Build') {
            steps {
                echo 'Building the application...'
                // Checkout code from Git
                checkout scm
                
                // Build Docker image
                sh 'docker build -t $DOCKER_IMAGE .'
                
                // Push to local registry or save for later stages
                sh 'docker save $DOCKER_IMAGE > webapp.tar'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Testing the application...'
                
                // Deploy to test server
                sh '''
                    scp -i $SSH_KEY -o StrictHostKeyChecking=no webapp.tar $TEST_SERVER:/tmp/
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $TEST_SERVER "sudo docker load < /tmp/webapp.tar"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $TEST_SERVER "sudo docker stop webapp-test || true"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $TEST_SERVER "sudo docker rm webapp-test || true"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $TEST_SERVER "sudo docker run -d -p 80:80 --name webapp-test $DOCKER_IMAGE"
                '''
                
                // Run basic health check
                sh '''
                    sleep 10
                    curl -s http://3.84.59.121 | grep -q "title"
                '''
            }
        }
        
        stage('Production') {
            when {
                expression { env.BRANCH_NAME == 'master' }
            }
            steps {
                echo 'Deploying to production...'
                
                // Deploy to production server
                sh '''
                    scp -i $SSH_KEY -o StrictHostKeyChecking=no webapp.tar $PROD_SERVER:/tmp/
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $PROD_SERVER "sudo docker load < /tmp/webapp.tar"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $PROD_SERVER "sudo docker stop webapp-prod || true"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $PROD_SERVER "sudo docker rm webapp-prod || true"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $PROD_SERVER "sudo docker run -d -p 80:80 --name webapp-prod $DOCKER_IMAGE"
                '''
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed'
            // Clean up workspace
            sh 'rm -f webapp.tar || true'
        }
        success {
            echo 'Pipeline successfully executed!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "hshar/webapp:${env.BUILD_NUMBER}"
        TEST_SERVER = "ubuntu@172.31.88.142"
        PROD_SERVER = "ubuntu@172.31.87.84"
        SSH_KEY = credentials('myssh_key') 
    }
    
    stages {
        stage('Build') {
            steps {
                echo 'Building the application...'
                // Checkout code from Git
                checkout scm
                
                // Build Docker image
                sh 'docker build -t $DOCKER_IMAGE .'
                
                // Push to local registry or save for later stages
                sh 'docker save $DOCKER_IMAGE > webapp.tar'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Testing the application...'
                
                // Deploy to test server
                sh '''
                    scp -i $SSH_KEY -o StrictHostKeyChecking=no webapp.tar $TEST_SERVER:/tmp/
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $TEST_SERVER "sudo docker load < /tmp/webapp.tar"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $TEST_SERVER "sudo docker stop webapp-test || true"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $TEST_SERVER "sudo docker rm webapp-test || true"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $TEST_SERVER "sudo docker run -d -p 80:80 --name webapp-test $DOCKER_IMAGE"
                '''
                
                // Run basic health check
                sh '''
                    sleep 10
                    curl -s http://3.84.59.121 | grep -q "title"
                '''
            }
        }
        
        stage('Production') {
            when {
                expression { env.BRANCH_NAME == 'master' }
            }
            steps {
                echo 'Deploying to production...'
                
                // Deploy to production server
                sh '''
                    scp -i $SSH_KEY -o StrictHostKeyChecking=no webapp.tar $PROD_SERVER:/tmp/
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $PROD_SERVER "sudo docker load < /tmp/webapp.tar"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $PROD_SERVER "sudo docker stop webapp-prod || true"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $PROD_SERVER "sudo docker rm webapp-prod || true"
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no $PROD_SERVER "sudo docker run -d -p 80:80 --name webapp-prod $DOCKER_IMAGE"
                '''
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed'
            // Clean up workspace
            sh 'rm -f webapp.tar || true'
        }
        success {
            echo 'Pipeline successfully executed!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}

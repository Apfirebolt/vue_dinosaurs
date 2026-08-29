pipeline {
    agent any

    environment {
        PORT = '3000'
        NODE_ENV = 'production'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                sh '''
                    echo "Cleaning previous build artifacts..."
                    rm -rf dist node_modules
                '''
            }
        }

        stage('Install & Build') {
            steps {
                sh '''
                    echo "Installing dependencies..."
                    npm ci

                    echo "Building Vite application..."
                    npm run build
                '''
            }
        }

        stage('Deploy & Serve') {
            steps {
                sh '''
                    echo "Starting or reloading Express server with PM2 on port 3000..."
                    pm2 startOrReload ecosystem.config.cjs || pm2 start server.js --name "vue-dinosaurs" --env PORT=3000
                    pm2 save
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment successful! App running on port 3000."
        }
        failure {
            echo "Build or deployment failed."
        }
    }
}
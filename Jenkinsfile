pipeline {
    agent any

    environment {
        NODE_IMAGE = 'node:18-alpine'
        NETLIFY_SITE_ID = 'cb2c3525-a650-47d5-a7b2-19003edab68c'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        CHROME_BIN = '/usr/bin/google-chrome-stable'

    stages {
        stage('Install Chrome') {
            agent {
                docker {
                    image 'your-custom-image-with-chrome'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo 'Installing Google Chrome...'
                    apt-get update && apt-get install -y google-chrome-stable
                    google-chrome-stable --version  # Verifica si Chrome se instaló correctamente
                '''
            }
        }

        stage('Build') {
            agent {
                docker {
                    image "${NODE_IMAGE}"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    echo '/*    /index.html   200' > dist/browser/_redirects
                    ls -la dist/browser
                '''
            }
        }

        stage('Unit tests') {
            agent {
                docker {
                    image "${NODE_IMAGE}"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo 'Running unit tests...'
                    npm install
                    npm test
                '''
            }
        }

        stage('Deploy staging') {
            agent {
                docker {
                    image "${NODE_IMAGE}"
                    reuseNode true
                }
            }
            steps {
                echo 'Starting Stage Build Stage'
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=dist/browser
                '''
            }
        }

        stage('Deploy prod') {
            agent {
                docker {
                    image "${NODE_IMAGE}"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=dist/browser --prod
                '''
            }
        }
    }
}

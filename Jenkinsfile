
cat > Jenkinsfile <<'EOF'
pipeline {
    agent any

    environment {
        PATH = "/Applications/Docker.app/Contents/Resources/bin:/usr/bin:/bin:/usr/sbin:/sbin"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/TenetiSrujana/BloodLink.git'
            }
        }

        stage('Verify Files') {
            steps {
                sh '''
                    test -f index.html
                    test -f styles.css
                    test -f Dockerfile
                    echo "All required project files found."
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t bloodlink:1.0 .'
            }
        }

        stage('Test Website') {
            steps {
                sh '''
                    docker run --rm bloodlink:1.0 nginx -t
                    echo "Website container configuration test passed."
                '''
            }
        }
    }

    post {
        success {
            echo 'BloodLink CI pipeline completed successfully.'
        }
        failure {
            echo 'BloodLink CI pipeline failed. Check the console output.'
        }
    }
}
EOF


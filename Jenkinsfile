
pipeline {
    agent any

    environment {
        PATH = "/Applications/Docker.app/Contents/Resources/bin:/usr/bin:/bin:/usr/sbin:/sbin"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Source code is checked out from GitHub by Jenkins.'
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

        stage('Deploy') {
            steps {
                sh '''
                    set -eu

                    if docker ps -a --format '{{.Names}}' | grep -qx 'bloodlink-app'; then
                        docker rm -f bloodlink-app
                    fi

                    docker run -d --name bloodlink-app -p 8081:80 bloodlink:1.0

                    for i in 1 2 3 4 5; do
                        if curl -fsS http://localhost:8081/ >/dev/null; then
                            echo "BloodLink deployment verified successfully."
                            exit 0
                        fi
                        sleep 2
                    done

                    echo "Deployment verification failed."
                    docker logs bloodlink-app
                    exit 1
                '''
            }
        }
    }

    post {
        success {
            echo 'BloodLink CI/CD pipeline completed successfully.'
        }
        failure {
            echo 'BloodLink pipeline failed. Check the console output.'
        }
    }
}


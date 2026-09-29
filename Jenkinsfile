pipeline {
    agent { label "dev" }
    stages {
        stage("code") {
            steps {
                git url: "https://github.com/snehasishpatra/two-tier-flask-app.git", branch: "master"
            }
        }
        stage("build") {
            steps {
                sh "docker build -t my-app:latest ." 
            }
        }
        stage("test") {
            steps {
                echo "code test"
            }
        }
        stage("push to docker hub") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "dockerHubCred",
                    passwordVariable: "dockerHubPass",
                    usernameVariable: "dockerHubUser"
                )]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker image tag my-app:latest \$dockerHubUser/two-tier-flask-app:latest"
                    sh "docker push \$dockerHubUser/two-tier-flask-app:latest"
                }
            }
        }
        stage('deploy') {
            steps {
                // FIXED: Wrapped in single quotes to pass variables cleanly to bash
                sh 'UID=$(id -u) GID=$(id -g) docker compose up -d --build --force-recreate flask-app'
            }
        }
    }
    post {
        success {
            // FIXED: Standardized formatting and corrected typos in "successful"
            emailext(
                body: "good news: your build is successful",
                subject: "build successful",
                to: "snehasishpatra932@gmail.com"
            )
        }
        failure {
            // FIXED: Added missing parentheses around parameters
            emailext(
                body: "bad news: your build is failed",
                subject: "build failed",
                to: "snehasishpatra932@gmail.com"
            )
        }
    }
}

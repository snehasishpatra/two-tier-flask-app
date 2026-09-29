@Library("Shared") _
pipeline {
    agent any
    stages {
        stage("code") {
            steps {
                git url: "https://github.com/snehasishpatra/two-tier-flask-app.git", branch: "master"
            }
        }
        stage("build") {
            steps {
                // Keep it named 'my-app' here
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

                    
                    // FIXED: References 'my-app:latest' and tags it correctly with ':latest' for the target repo
                    sh "docker image tag my-app:latest \$dockerHubUser/two-tier-flask-app:latest"
                    
                    sh "docker push \$dockerHubUser/two-tier-flask-app:latest"
                }
            }
        }
        stage('deploy') {
            steps {
                sh "UID=\$(id -u) GID=\$(id -g) docker compose up -d --build --force-recreate flask-app"
            }
        }
    }
}

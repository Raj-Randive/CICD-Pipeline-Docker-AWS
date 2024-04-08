pipeline{
    agent any
    
    stages{
        stage("Clone Code"){
            steps{
                echo "Cloning the Code"
                git url:"https://github.com/Raj-Randive/CICD-Pipeline-Docker-AWS.git", branch: "main"
            }
        }
        stage("Build"){
            steps{
                echo "Building the image."
                sh "docker build -t sample-website ."
            }
        }

        stage("SonarQube Analysis") {
            steps {
                script {
                    def scannerHome = tool name: 'sonarqube-server'
                    withSonarQubeEnv('sonarqube-server') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        stage("Push to DockerHub"){
            steps{
                echo "Pushing the image to DockerHub."
                withCredentials([usernamePassword(credentialsId: "dockerHub", passwordVariable: "dockerHubPass", usernameVariable: "dockerHubUser")]){
                    sh "docker tag sample-website ${env.dockerHubUser}/sample-website:latest"
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/sample-website:latest"
                }
            }
        }

        stage("Deploy"){
            steps{
                echo "Deploying the container."
                sh "docker-compose down && docker-compose up -d"
            }
        }
    }
}
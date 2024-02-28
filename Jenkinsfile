pipeline{
    agent any
    
    parameters{
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests ? ')
        string(name: 'TAG_NAME', defaultValue: 'latest', description : 'Enter the image tag name: ')
    }
    
    stages{
        
        stage("code checkout"){
            steps{
                git url: "https://github.com/ShailedraSharma/jenkins-cicd.git", branch: "master"
                echo "code cloned"
                bat "dir" // Windows command to list files in the directory
            }
        }
        
        stage("Running test cases"){
            when{
                expression { params.RUN_TESTS }
            }
            steps{
                echo "Running test cases"
            }
        }

        stage("Performing code analysis"){
            steps{
                echo "code analysis complete"
            }
        }
        
        stage("Building docker image"){
            steps{
                bat "docker build -t node-todo-app:${params.TAG_NAME} ."
            }
        }

        stage("Performing image scanning"){
            steps{
                echo "image scanning completed"
            }
        }
        
        stage("Pushing image to DockerHub"){
            steps{
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                    bat "docker login -u %dockerHubUser% -p %dockerHubPass%"
                    bat "docker tag node-todo-app:${params.TAG_NAME} %dockerHubUser%/node-todo-app:${params.TAG_NAME}"
                    bat "docker push %dockerHubUser%/node-todo-app:${params.TAG_NAME}"
                }
            }
        }
        
        stage("Deploying docker image"){
            steps{
                withKubeConfig([credentialsId: 'minikubeConfig']) {
                    bat "kubectl apply -f deployment.yaml"
            }
        }
        }
        
    }
}

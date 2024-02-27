pipeline{
    agent any
    
    parameters{
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests ? ')
        string(name: 'TAG_NAME', defaultValue: '', description : 'Enter the image tag name: ')
    }
    
    stages{
        
        stage("code checkout"){
            steps{
                git url: "https://github.com/ShailedraSharma/jenkins-cicd.git", branch: "master"
                echo "code cloned"
                sh "ls -ltr"
            }
        }
        
        stage("Running test casses"){
            when{
                expression { params.RUN_TESTS }
            }
            steps{
                echo "Running test casses"
                }
            }
        
        stage("Building docker image"){
            steps{
                sh "docker build -t node-todo-app:${params.TAG_NAME} ."
            }
        }
        
        stage("Pushing image to dockerhub"){
            steps{
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker tag node-todo-app:${params.TAG_NAME} ${env.dockerHubUser}/node-todo-app:${params.TAG_NAME}"
                    sh "docker push ${env.dockerHubUser}/node-todo-app:${params.TAG_NAME}"
                }
            }
        }
        
        stage("Deploying docker image"){
            steps{
                sh "docker-compose down && docker-compose up -d"
                echo "container is running"
            }
        }
    }
    
}

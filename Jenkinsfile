pipeline{
    agent any
    
    parameters{
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests ? ')
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
                bat "npm install && npm audit fix --force && npm run test"
            }
        }
        
        stage("Building docker image"){
            steps{
                bat "docker build -t node-todo-app:${BUILD_NUMBER} ."
            }
        }

        stage("Performing image scanning"){
            steps{
                bat "trivy -f table -o result.txt image node-todo-app:${BUILD_NUMBER}"
            }
        }
        
        stage("Pushing image to DockerHub"){
            steps{
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                    bat "docker login -u %dockerHubUser% -p %dockerHubPass%"
                    bat "docker tag node-todo-app:${BUILD_NUMBER} %dockerHubUser%/node-todo-app:${BUILD_NUMBER}"
                    bat "docker push %dockerHubUser%/node-todo-app:${BUILD_NUMBER}"
                }
            }
        }
        
        stage("Deploying docker image"){
            steps{
                withKubeConfig([credentialsId: 'minikubeConfig']) {
                    script{
                        def TAG_NAME="${BUILD_NUMBER}"
                         powershell """
                        (Get-Content deployment.yaml) -replace 'TAG_NAME', '${TAG_NAME}' | Set-Content deployment.yaml
                        """
                        bat "type deployment.yaml"
                        bat "kubectl apply -f deployment.yaml -n devlopment"
                    }
                }
            }
        }
    }
    post{
            always{
                emailext(
                body: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS: Check console output at $BUILD_URL to view the results.', 
                subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', 
                to: 'shailendra.761979@gmail.com'
                )
            }
        }
}

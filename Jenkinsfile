    pipeline{
        agent any
     stages{
        stage("Checkout"){
            steps{
                    git branch: 'main', credentialsId: 'git_hub_cred_id', url: 'https://github.com/govardhan34445/test-repo-ecr.git'

            }
        }
        stage("Build Docker Image"){
            steps{

             sh """
             docker build -t test_image:${BUILD_NUMBER} .
             """
            }
        }
        stage("Push to ecr registry"){
            steps{
                sh """
                export AWS_ACCESS_KEY_ID=AKIA36GKNQJI2Y5PIOVC && export AWS_SECRET_ACCESS_KEY=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
                aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/e5k4j6y8
                docker tag test_image:${BUILD_NUMBER} public.ecr.aws/e5k4j6y8/test-ecr:${BUILD_NUMBER}
                docker push public.ecr.aws/e5k4j6y8/test-ecr:${BUILD_NUMBER}
                """
            }
        }
        stage("Deploy container in server"){
            steps{
            sshagent(['46d73466-13b7-4ec1-8a48-79278bde3816']) {
                    sh """
                      docker pull public.ecr.aws/e5k4j6y8/test-ecr:${BUILD_NUMBER}
                      docker stop container1
                      docker rmi container1
                      docker run -d -p 8080:8080 --name container1 public.ecr.aws/e5k4j6y8/test-ecr:${BUILD_NUMBER} 
                      """
            }
            }
        }
        }
        }

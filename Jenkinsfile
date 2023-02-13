    pipeline{
            agent { node { label "slave1" }}
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
                    script {

                 def USER_INPUT = input(
                    message: 'User input required - Do you want to proceed?',
                    parameters: [
                            [$class: 'ChoiceParameterDefinition',
                             choices: ['no','yes'].join('\n'),
                             name: 'input',
                             description: 'Menu - select box option']
                    ])
                    echo "The answer is: ${USER_INPUT}"
                    if( "${USER_INPUT}" == "yes"){
                        sshagent(['46d73466-13b7-4ec1-8a48-79278bde3816']) {
                                sh """
                                docker pull public.ecr.aws/e5k4j6y8/test-ecr:\${BUILD_NUMBER}
                        
                                docker run -d -p 8090:80 --name container1 public.ecr.aws/e5k4j6y8/test-ecr:\${BUILD_NUMBER} 
                    
                                """
                        }
                   }
                else {
                   println "Skipping deployment"
                }
            
            }
            }
        }
        stage("Post Build Actions"){
            steps{
            
                    sh """
                      docker rmi public.ecr.aws/e5k4j6y8/test-ecr:\${BUILD_NUMBER}
                      """
            }
        }
        }
post{

        success{
        emailext to: 'govardhanr992@gmail.com',
                subject: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}.",
                body: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}.",
                replyTo: 'govardhanr992@gmail.com'
        }
 
        failure{
        emailext to: 'govardhanr992@gmail.com',
                subject: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}.",
                body: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}.",
                replyTo: 'govardhanr992@gmail.com'
        }
 
}
        }

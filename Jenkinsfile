//this pipeline updates the deployment files using jenkins pipeline . the updated files are then pushed to git which triggers argo cd to deploy them on the destination k8 cluster
pipeline{
    agent{  //the agent is the vm that the all the dependencies for the jenkins pipeline to run in will be installed . in production , use a dedicated agent
         //label "built-in"
         label "jenkins-agent"
        //docker { image 'node:20.10.0-alpine3.19' }
    }
    
    environment {
        APP_NAME = "production-pipeline "
        
    }
    stages{
        stage("Cleanup Workspace"){
            steps {
                cleanWs()
            }

        }
    
        stage("Checkout from SCM"){//check out the code from git repo into the jenkins workspace 
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/techinanutshellhack/application.git'
            }

        }

        stage("Update the Deployment Tag"){//build application 
            steps {
                sh """
                    cat deployment.yaml
                    sed -i "s/${APP_NAME}/${APP_NAME}:${IMAGE_TAG}/g" "deployment.yaml"
                    cat deployment.yaml
                """
                //sed -i "s/$search_string/$replace_string/g" "$file"
            }
//sed -i 's/${APP_NAME}/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
        }

        stage("Push the changed deploymentfile to Git"){// test application with maven
            steps {
                sh """
                git config --global user.name "sweetpeaito"
                git config --global user.email "itohaneregie@gmail.com"
                git add deployment.yaml
                git commit -m "updated deployment manifest"

                """
                withCredentials({gitUsernamePassword(credentialsId: 'github-pat',gitToolName: 'Default')}){
                    sh "git push https://github.com/techinanutshellhack/application main"
                }
            }

        }
        
     }
}

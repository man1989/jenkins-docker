def REGISTRY = "registry.local.innolab.in"
pipeline{
    agent{
        kubernetes {
            yamlFile "builder.yml"
        }
    }
    stages {
        stage("Checkout from SCM"){
            steps {
                git branch: "git-update",  url: "https://github.com/man1989/jenkins-docker.git"
            }
        }
        stage("Build and Push image"){
            steps{
                container("kaniko"){
                    sh """
                      /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=${REGISTRY}/flash-server:latest
                    """
                }
            }
        }
        stage("Update version"){
            steps{
                withCredentials([gitUsernamePassword(credentialsId: "jenkins-demo")]){
                    sh '''
                        sed -i -e "/version/s/[^.]*$/${BUILD_NUMBER}/" manifest.json
                        git config user.name "Jenkins"
                        git config user.email "jenkins@innolab.in"
                        git add manifest.json
                        git commit -m 'version updated'
                        git push origin git-update
                    '''
                }
            }
        }
    }
}

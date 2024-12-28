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
                git branch: "main",  url: "https://github.com/man1989/jenkins-docker.git"
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
    }
}

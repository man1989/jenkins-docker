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
        stage("Bump package version") {
            steps {
                sh "ls"
                sh "sed -i -e \"/\"version\"/s/[^.]*\$/${BUILD_NUMBER}\\\"/\" `pwd`/manifest.json"
                script {
                  def result = sh(returnStdout: true, script: "cat manifest.json | grep version | awk -F'\"' '{print \$4}'")
                  env.APP_VERSION = result
                }
                sh "cat manifest.json"
            }
        }        
        stage("Build and Push image"){
            steps{
                container("kaniko"){
                    sh "echo ${env.APP_VERSION}"
                    sh "/kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=${REGISTRY}/flash-server:${env.APP_VERSION}"
                }
            }
        }
        stage("Update version"){
            steps{
                withCredentials([gitUsernamePassword(credentialsId: "jenkins-demo")]){
                    sh """
                        git config user.name "Jenkins"
                        git config user.email "jenkins@innolab.in"
                        git add manifest.json
                        git commit -m 'version updated'
                        git push origin git-update
                    """
                }
            }
        }
    }
}

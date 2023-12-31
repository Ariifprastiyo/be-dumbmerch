def branch = "CI/CD"
def repo = "git@github.com:Ariifprastiyo/be-dumbmerch.git"
def cred = "dumbmerch"
def dir = "~/be-dumbmerch"
def server = "arif@103.23.199.89"
def imagename = "be1dumbmerch"
def dockerusername = "ariif"

pipeline {
    agent any

    stages {
        stage('Repository pull') {
            steps {
                sshagent([cred]) {
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                                mkdir ${dir}
                        cd ${dir}
                                    git init
                        git remote add origin ${repo}
                                    git pull origin master
                                    git branch ${branch}
                                    git checkout ${branch}
                                    git pull origin ${branch}
                        exit
                        EOF
                    """
                }
            }
        }

    stage('Image build') {
            steps {
                sshagent([cred]) {
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                        cd ${dir}
                        docker build -t ${imagename}:latest .
                        exit
                        EOF
                    """
                }
            }
        }

        stage('Running the image in a container') {
            steps {
                sshagent([cred]) {
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                        cd ${dir}
                        docker container stop ${imagename}
                        docker container rm ${imagename}
                        docker run -d -p 3000:3000 --name="${imagename}"  ${imagename}:latest
                        docker container stop ${imagename}
                        docker container rm ${imagename}
                        exit
                        EOF
                    """
                }
            }
        }

        stage('Image push') {
            steps {
               sshagent([cred]) {
                            sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                                    docker tag ${imagename}:latest ${dockerusername}/${imagename}:latest
                                    docker image push ${dockerusername}/${imagename}:latest
                                    docker image rm ${dockerusername}/${imagename}:latest
                                    docker image rm ${imagename}:latest
                                    exit
                    EOF
                        """
                        }
            }
        }
    }
}

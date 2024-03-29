//  GroupName: TeamPRTV (Preethi Ranganathan, Mary Rithika Reddy Gade, Tarun Vinay Gujjar, Vikas Halgar Seetharam)
//  Jenkinsfile defines the pipeline in scm and here, there are 4 stages in
//  this CICD pipeline: Build, Docker build, Push to Docker Hub, Kubectl Get All Nodes, Deploying Rancher to single node

@NonCPS
def generateTag() {
    return new Date().format('yyyyMMdd-HHmmss')
}
// Pipeline Stages
pipeline {
    environment {
        registry = 'tarungujjar/survey'
        registryCredential = '8ce51330-a1b1-4452-b526-5677a1d0a9d0'
    }
    agent any

    stages {
        //  Build Stage
        stage('Build') {
            steps {
                script {
                    checkout scm
                    sh 'rm -rf *.war'
                    sh 'jar -cvf survey.war -C src/main/webapp/ . '
                    // sh 'echo ${BUILD TIMESTAMP}'
                    tag = generateTag()
                    sh 'echo $tag'
                }
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        def customImage = docker.build('tarungujjar/survey:' + tag)
                    }
                }
            }
        }
        // Push to DockerHub Stage
        stage('Push to Docker Hub') {
            steps {
                script {
                    // sh 'echo ${BUILD_TIMESTAMP}'
                    docker.withRegistry('', registryCredential) {
                        def image = docker.build('tarungujjar/survey:' + tag, '.')
                        docker.withRegistry('', registryCredential) {
                            image.push()
                        }
                    }
                }
            }
        }
        stage('Kubectl Get All Nodes') {
            steps {
                script {
                    sh 'kubectl get deployments'
                }
            }
        }

        // Deploying Rancher to single node
        stage('Deploying Rancher to single node') {
            steps {
                script {
                	//sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl set image deployment/swe645-deployment -n swe645-namespace container-0=tarungujjar/survey:' + tag
                }
            }
        }
    }
}
pipeline {
    agent any

    options {
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5'))
        disableConcurrentBuilds()
        timestamps()
    }

    parameters {
  choice choices: ['dev', 'master', 'prod'], name: 'BranchName'
}


    tools {
        maven "maven-3.9.6"
    }

    environment {
        WORKSPACE = "/var/lib/jenkins/workspace" // Update this with the actual path
        DOCKERHUB_CREDENTIALS = credentials('dockerloginid')
        DOCKER_IMAGE_NAME = "manjunathachar/regapp"
        DOCKER_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Clean up') {
            steps {
                script {
                    sh "docker rm -f ${DOCKER_IMAGE_NAME}:${DOCKER_TAG} || true"
                    sh "docker rmi ${DOCKER_IMAGE_NAME}:${DOCKER_TAG} || true"
                }
            }
        }

        stage('Delete workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Cloning the project or SCM checkout') {
            steps {
                git credentialsId: 'github_credentials', url: 'https://github.com/manju65char/DevOps-CICD-Sample-Project.git'
            }
        }

        stage('Maven build') {
            steps {
                echo "Compiling and creating artifacts: .jar or .war"
                sh "mvn clean package"
            }
        }

        stage('Static code analysis') {
            steps {
                echo "This stage is used to generate project reports based on quality profile and quality gates"
                //sh "mvn sonar:sonar"
            }
        }

        stage('Deploy the artifact into Nexus server') {
            steps {
                echo "This stage is used to deploy the jar or war file into Nexus server"
                // Implement deployment to Nexus if needed
            }
        }

        stage('Deploy to Tomcat server') {
            steps {
                echo "Deploying the artifact to Tomcat"
                sh "cp ${WORKSPACE}/sample_banking_project/webapp/target/*.war /opt/tomcat/webapps/"
            }
        }

        stage("Docker build") {
            steps {
                echo "Building Docker image"
                sh "docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_TAG} ."
                sh "docker image list"
                sh "docker tag ${DOCKER_IMAGE_NAME}:${DOCKER_TAG} ${DOCKER_IMAGE_NAME}:latest"
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerloginid', passwordVariable: 'DOCKERHUB_CREDENTIALS_PSW', usernameVariable: 'DOCKERHUB_CREDENTIALS_USR')]) {
                    sh "echo \${DOCKERHUB_CREDENTIALS_PSW} | docker login -u \${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                }
            }
        }

        stage('Docker Image Scanning') {
            steps {
                echo "Scanning Docker image using Trivy"
                sh "docker pull ${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"
                sh "docker run --rm -v ${WORKSPACE}:/workspace aquasec/trivy --output /workspace/trivy_report.json ${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"
            }
        }

        stage('Save Scan Report') {
            steps {
                echo "Saving Trivy scan report"
                sh "cp ${WORKSPACE}/trivy_report.json ${WORKSPACE}/archive/"
            }
        }

        stage('Approve - push Image to Docker Hub') {
            steps {
                script {
                    env.APPROVED_DEPLOY = input message: 'User input required. Choose "yes" to approve or "Abort" to reject', ok: 'Yes', submitterParameter: 'APPROVER'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Pushing Docker image to Docker Hub"
                sh "docker push ${DOCKER_IMAGE_NAME}:latest"
            }
        }

        stage('Approve - Deployment to Kubernetes Cluster') {
            steps {
                script {
                    env.APPROVED_DEPLOY_KUBE = input message: 'User input required. Choose "yes" to approve or "Abort" to reject', ok: 'Yes', submitterParameter: 'APPROVER_KUBE'
                }
            }
        }

        /*
        stage('Deploy to Kubernetes Cluster') {
            steps {
                script {
                    sshPublisher(publishers: [sshPublisherDesc(configName: 'kube_masternode', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f k8sdeployment.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'k8sdeployment.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                }
            }
        }
        */
    }
}


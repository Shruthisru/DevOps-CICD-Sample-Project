pipeline {
    agent any // by default the projects build in the Jenkins master server, in slave or node server

    tools {
        maven "maven-3.9.6" // make sure the Maven version is specified in tools/maven installations -> maven-3.9.6
    }

    environment {
        WORKSPACE = "your Jenkins home dir path"
        DOCKERHUB_CREDENTIALS = credentials('dockerloginid')
    }

    stages {
        stage('Cloning the project or SCM checkout') {
            steps {
                // here we are going to write codes or commands
                git branch: 'master', credentialsId: 'git_credentials', url: 'https://github.com/manju65char/DevOps-CICD-Sample-Project.git'
            }
        }

        stage('Maven build') {
            steps {
                // here we are going to write codes or commands
                echo "compiled and created the artifacts: .jar or war"
                sh "mvn clean package" // (batch)bat "mvn clean package" -> in Windows
            }
        }

        stage('Static code analysis') {
            steps {
                // here we are going to write codes or commands
                echo "this stage is used to generate the project report based on quality profile and quality gates"
                //sh "mvn sonar:sonar"
            }
        }

        stage('Deploy the artifact into Nexus server') {
            steps {
                // here we are going to write codes or commands
                echo "this stage is used to deploy the jar or war file into Nexus server"
            }
        }

        stage('Deploy to Tomcat server') {
            steps {
                echo "Deploying the artifact to Tomcat"
                sh "cp /var/lib/jenkins/workspace/sample_banking_project/webapp/target/*.war /opt/tomcat/webapps/"
            }
        }

        stage("Docker build") {
            steps {
                sh 'docker version'
                sh "docker build -t manjunathachar/regapp:${BUILD_NUMBER} ."
                sh 'docker image list'
                sh "docker tag manjunathachar/regapp:${BUILD_NUMBER} manjunathachar/insuranceapp:latest"
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Approve - push Image to Docker Hub') {
            steps {
                //----------------send an approval prompt-------------
                script {
                    env.APPROVED_DEPLOY = input message: 'User input required. Choose "yes" to approve or "Abort" to reject', ok: 'Yes', submitterParameter: 'APPROVER'
                }
                //-----------------end approval prompt------------
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh "docker push manjunathachar/insuranceapp:latest"
            }
        }

        stage('Approve - Deployment to Kubernetes Cluster') {
            steps {
                //----------------send an approval prompt-----------
                script {
                    env.APPROVED_DEPLOY_KUBE = input message: 'User input required. Choose "yes" to approve or "Abort" to reject', ok: 'Yes', submitterParameter: 'APPROVER_KUBE'
                }
                //-----------------end approval prompt------------
            }
        }
    }
}


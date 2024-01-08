pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path..
        maven "maven-3.9.6"
    }

    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                git url: 'https://github.com/manju65char/DevOps-CICD-Sample-Project.git'
            }
        }

        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
    }
}

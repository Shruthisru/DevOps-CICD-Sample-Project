pipeline{
agent any // by default the projects build in the jenkins master server , in slave or node server 

tools{ 
 maven "maven-3.9.6"     // make sure the maven version which you specified in tools/maven installations -> maven-3.9.6
}

environment{
 WORKSPACE = "your jenkins home dir path "
 DOCKERHUB_CREDENTIALS = "DJDJDJSLJDLJL"

}

    stages{
 
           stage('cloning the project or SCM checkout'){
                steps{
                 // here we are going write codes or commands 
                 git branch: 'master', credentialsId: 'git_credentials', url: 'https://github.com/manju65char/DevOps-CICD-Sample-Project.git'
                }
      
           }
           
           stage('Maven build'){
                steps{
                 // here we are going write codes or commands 
                 echo "compiled and created the artifacts: .jar or war "
                 sh "mvn clean package"    // (batch)bat "mvn clean package" -> in windowos 
                }
      
           }
           stage('static code anaysis'){
                steps{
                 // here we are going write codes or commands 
                 echo "this stage is used to generate the project report based on quality profile and quality gates"
                 //sh "mvn sonar:sonar"
                }
      
           }
           stage('deploy the artifact into Nexus server'){
                steps{
                 // here we are going write codes or commands 
                 echo " this stage is used to deploy the jar or war file into nexus server"
                }
      
           }
           
           stage('Deploy to Tomcat server') {
            steps {
                echo "Deploying the artifact to Tomcat"
                sh "cp /var/lib/jenkins/workspace/sample_banking_project/webapp/target/*.war /opt/tomcat/webapps/"
            }
        }
    }
}

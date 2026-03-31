pipeline{
    agent any
    
    tools{
        maven 'maven3'
    }
    
    stages{
        stage('git checkout'){
            steps{
                git 'https://github.com/ganeshSDevops/java-project-maven-new.git'
            }
        }
        
        stage('compile,test and build code'){
            steps{
                sh '''
                mvn clean package
                '''
            }
        }
        
        stage('sonarQube-analysis'){
            steps{
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar'
                }
            }
        }
        
        stage('sonar quality check'){
            steps{
                timeout(time:1 ,unit:'MINUTES'){
                    waitForQualityGate abortPipeline:false
                }
            }
        }
        
        stage('add artifact on s3'){
            steps{
                sh 'aws s3 cp target/myapp.war s3://jenkins-war-artifact-bucket/' 
            }
        }
        
        stage('add artifact on nexus'){
            steps{
                nexusArtifactUploader artifacts: [[artifactId: 'myapp', classifier: '', file: 'target/myapp.war', type: 'war']], credentialsId: 'nexusCred', groupId: 'in.reyaz', nexusUrl: '3.85.183.170:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'hotstar-app', version: '8.3.3-SNAPSHOT'
            }
        }
        
        stage('deploy to tomcat'){
            steps{
                deploy adapters: [tomcat9(alternativeDeploymentContext: '', credentialsId: 'tomcatCred', path: '', url: 'http://44.222.113.142:8080')], contextPath: 'hotstar-app', war: '**/*.war'
            }
        }
    }
}

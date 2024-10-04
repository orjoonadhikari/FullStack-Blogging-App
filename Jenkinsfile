pipeline {
    agent any
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/orjoonadhikari/FullStack-Blogging-App.git'
            }
        }
        stage('compile') {
            steps {
                echo 'Hello World'
                sh "mvn compile" 
            }
        }
        stage('Test') {
            steps {
                echo 'Hello World'
                sh "mvn test"
            }
        }
        stage('trivy fs scan') {
            steps {
                echo 'Hello World'
                sh "trivy fs --format table -o fs.html ."
            }
        }
        stage('sonarqube') {
            steps {
                echo 'Hello World'
                withSonarQubeEnv('sonar-server') {
                // some block
                sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-app -Dsonar.projectKey=Blogging-app -Dsonar.java.binaries=target '''
                }
            }
        }
        stage('build step') {
            steps {
                echo 'Hello World'
                sh "mvn package"
            }
        }
        stage('publish artifacts') {
            steps {
                echo 'Hello World'
                withMaven(globalMavenSettingsConfig: 'mavan-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                // some block
                sh "mvn deploy"
                }
            }
        }
        stage('docker build and tag') {
            steps {
                echo "Pushing to docker hub"
                withCredentials([usernamePassword(credentialsId:"docker",passwordVariable:"dockerHubpass",usernameVariable:"dockerHubUser")]){
                sh "docker login -u ${dockerHubUser} -p ${dockerHubpass}"
                sh "docker build -t blog-app:latest ."
                
                }
            }
        }
        
        stage('trivy image scan') {
            steps {
                sh "trivy image --format table -o image.html arjunadhikari19/blog-app:latest"
            }
        }
        stage('docker push') {
            steps {
                withCredentials([usernamePassword(credentialsId:"docker",passwordVariable:"dockerHubpass",usernameVariable:"dockerHubUser")]){
                sh "docker login -u ${dockerHubUser} -p ${dockerHubpass}"
                sh 'docker tag blog-app ${dockerHubUser}/blog-app:${BUILD_ID}'
                sh 'docker push ${dockerHubUser}/blog-app:${BUILD_ID}'
                
                }
            }
        }
    }
}

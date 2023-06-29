pipeline {
    agent any
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }


    stages {
        stage('Git checkout') {
            steps {
                 git branch: 'main', changelog: false, poll: false, url: 'https://github.com/bhavanishankar26/shopping-cart-shankar.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn clean compile"
                
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                        sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://http://65.0.120.20:9000/ -Dsonar.login=squ_da8d546d3320e2faeeef69d2f5e259e21075a86c -Dsonar.projectName=shopping-cart \
            -Dsonar.java.binaries=. \
            -Dsonar.projectKey=shopping-cart'''
                  }
        }
        
        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
          stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        
        stage('Docker Build & Push') {
    steps {
        script {
            withDockerRegistry(credentialsId: 'f0d72c9f-c017-41dd-8222-9dcfe88f669f', toolName: 'docker') {
                sh "docker build -t shopping-cart:latest -f docker/Dockerfile ."
                sh "docker tag shopping-cart:latest gadebhavani26/shopping-cart:latest"
                sh "docker push gadebhavani26/shopping-cart:latest"
            }
        }
    }
        }
        
        stage('Deploying App to Kubernetes') {
         steps {
           script {
             kubernetesDeploy(configs: "deploymentservice.yml", kubeconfigId: "Kubernetes")
         }
      }
    }
        
        
    }
}

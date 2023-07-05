pipeline {
    agent any
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_DEFAULT_REGION = "ap-south-1"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/bhavanishankar26/Shopping-Cart.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn clean compile"
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://13.232.230.24:9000/ -Dsonar.login=squ_1cbd96bd9e4fc6f853f11ca0fc9d92dd6a66de61 -Dsonar.projectName=shopping-cart \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=shopping-cart '''
            }
        }
        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
          stage('Build App') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'Docker', toolName: 'docker') {
                    sh "docker build -t shopping-cart:latest -f docker/Dockerfile ."
                    sh "docker tag shopping-cart:latest gadebhavani26/shopping-cart:latest"
                    sh "docker push gadebhavani26/shopping-cart:latest"
                    }
                }
            }
        }
        stage("Deploy to EKS") {
            steps {
                script {
                    dir('.') {
                        sh "aws eks --region ap-south-1 update-kubeconfig --name terraform-eks-demo"
                        sh "kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.0/deploy/static/provider/cloud/deploy.yaml"
                        sh "kubectl apply -f deploymentservice.yml"
                    }
                }
            }
        }
    }
}

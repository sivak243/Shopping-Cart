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
        stage('Deploy to Artifactory') {
    environment {
        // Define the target repository in Artifactory
        TARGET_REPO = 'my-local-repo'
    }
    
    steps {
        script {
            try {
                def server = Artifactory.newServer url: 'http://52.66.152.113:8082/artifactory/my-local-repo/', credentialsId: 'jfrog-cred'
                def uploadSpec = """{
                    "files": [
                        {
                            "pattern": "target/*.jar",
                            "target": "${TARGET_REPO}/"
                        }
                    ]
                }"""
                
                server.upload(uploadSpec)
            } catch (Exception e) {
                // Print the error details to the console output
                echo "Failed to deploy artifacts to Artifactory: ${e.getMessage()}"
                // Mark the build as failed
                error("Failed to deploy artifacts to Artifactory")
            }
        }
    }
}

        // stage('Deploy to Artifactory') {
        //     environment {
        //         // Define the target repository in Artifactory
        //         TARGET_REPO = 'my-local-repo'
        //     }
            
        //     steps {
        //         script {
        //             try {
        //                 def server = Artifactory.newServer url: 'http://52.66.152.113:8082/artifactory/my-local-repo/', credentialsId: 'jfrog-cred'
        //                 def uploadSpec = """{
        //                     "files": [
        //                         {
        //                             "pattern": "target/*.jar",
        //                             "target": "${TARGET_REPO}/"
        //                         }
        //                     ]
        //                 }"""
                        
        //                 server.upload(uploadSpec)
        //             } catch (Exception e) {
        //                 error("Failed to deploy artifacts to Artifactory: ${e.message}")
        //             }
        //         }
        //     }
        // }
        
//         stage('Sonarqube Analysis') {
//             steps {
//                         sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://55.66.152.113:9000/ -Dsonar.login=squ_1cbd96bd9e4fc6f853f11ca0fc9d92dd6a66de61 -Dsonar.projectName=shopping-cart \
//                         -Dsonar.java.binaries=. \
//                         -Dsonar.projectKey=shopping-cart '''
//             }
//         }
//         stage('OWASP Scan') {
//             steps {
//                 dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP'
//                 dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
//             }
//         }
        
//           stage('Build App') {
//             steps {
//                 sh "mvn clean package -DskipTests=true"
//             }
//         }
        
//         stage('Docker Build & Push') {
//             steps {
//                 script {
//                     withDockerRegistry(credentialsId: 'Docker', toolName: 'docker') {
//                     sh "docker build -t shopping-cart:latest -f docker/Dockerfile ."
//                     sh "docker tag shopping-cart:latest gadebhavani26/shopping-cart:latest"
//                     sh "docker push gadebhavani26/shopping-cart:latest"
//                     }
//                 }
//             }
//         }
//         stage("Deploy to EKS") {
//             steps {
//                 script {
//                     dir('.') {
//                         sh "aws eks --region ap-south-1 update-kubeconfig --name terraform-eks-demo"
//                         sh "kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.0/deploy/static/provider/cloud/deploy.yaml"
//                         sh "kubectl apply -f deploymentservice.yml"
//                     }
//                 }
//             }
//         }
    }
}

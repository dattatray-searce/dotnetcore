pipeline { 
    environment { 
        PROJECT_ID = 'clinic-to-cloud'
        LOCATION = 'us-central1-a'
        CREDENTIALS_ID = 'poc-searce'
        CLUSTER_NAME = 'poc-searce'
        registry = "docsearce/dotnetcore" 
        registryCredential = 'dockerhub' 
    }
    agent any 
    stages { 
        stage('Cloning our Git') { 
            steps { 
                git branch: 'master', url:'https://github.com/dattatray-searce/dotnetcore.git' 
            }
        } 
        stage('Building our image') { 
            steps {  
                script { 
                    dockerImage = docker.build("${registry}:${env.BUILD_ID}")
                }
            } 
        }
        stage('Deploy our image') { 
            steps { 
                script { 
                      docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                            dockerImage.push("${env.BUILD_ID}")
                    }
                } 
            }
        } 
         stage('Deploy to GKE test cluster') {
             
            steps{
                echo "Deployment started ..."
                sh "sed -i 's/dotnetcore:latest/dotnetcore:${BUILD_NUMBER}/g' deployment.yaml"
                echo "KubernetesEngineBuilder started ... ${PATH}"
                step([$class: 'KubernetesEngineBuilder', 
                    projectId: env.PROJECT_ID, 
                    clusterName: env.CLUSTER_NAME, 
                    location: env.LOCATION, 
                    manifestPattern: 'deployment.yaml', 
                    credentialsId: env.CREDENTIALS_ID, 
                    verifyDeployments: true
                ])
            }
        }  
    }
}

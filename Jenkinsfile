pipeline {
    agent any
      environment {
        DOCKER_IMAGE = 'arya023/apachewebsite:v1.0'
        KUBECONFIG = credentials('kubeconfig')
    }

    stages {
        stage('Clone Git repository') {
            steps {
                 git 'https://github.com/Aryachaurasia23/apachewebsite/'
            }
        }
        stage('run ansibleplaybook'){
          steps{
            ansiblePlaybook credentialsId: 'ansible-ssh', installation: 'ansible2', inventory: 'inventory.ini', playbook: 'installapche.yml', vaultTmpPath: ''
          }
        }
       stage('Docker Build & Push') {
        steps {
            script {
                withDockerRegistry([credentialsId: 'docker', url: 'https://index.docker.io/v1/']) {
                    sh '''
                    echo "Building Docker image..."
                    docker build --no-cache -t $DOCKER_IMAGE -f Dockerfile .
                    echo "Pushing Docker image to Docker Hub..."
                    docker push $DOCKER_IMAGE
                    '''
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script{
                     kubernetesDeploy (configs: 'deployment.yml' ,kubeconfigId: 'k8sconfig')
                   
                    }
                }
            }
        }
    }





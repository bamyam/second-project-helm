pipeline {
    agent { label 'agent' }
    
    environment {
        DOCKER_IMAGE_OWNER = 'bamyam'
    }
    
    stages {
        stage('init') {
            steps {
                sh 'rm -rf *'
            }
        }
        stage('Checkout') {
            steps {
                sh '''
                git clone https://github.com/bamyam/second-project-msa.git
                '''
            }
        }
        
        stage('Docker Image Build') {
            steps {
                echo "${env.Build_number}"
                sh """
                cd second-project-msa
                sed -i "/ENV PYTHONPATH=\\/app/a ENV userid=clouds \\nENV passwd=clouds \\nENV dbname=clouds \\nENV dbhost=13.125.245.89 \\nENV GMAIL_PASS='zpcb gtbm baks hxlh'" ./admin-service/Dockerfile
                sed -i "/ENV PYTHONPATH=\\/app/a ENV userid=clouds \\nENV passwd=clouds \\nENV dbname=clouds \\nENV dbhost=13.125.245.89 \\nENV GMAIL_PASS='zpcb gtbm baks hxlh'" ./visitor-service/Dockerfile
                sed -i "s/\\"http:\\/\\/43.203.228.137:32321\\"/\\"http:\\/\\/43.203.228.137:32321\\",\\n\\"http:\\/\\/ttttt-1505394909.ap-northeast-2.elb.amazonaws.com\\"/g" ./admin-service/api/main.py
                sed -i "s/\\"http:\\/\\/43.203.228.137:32321\\"/\\"http:\\/\\/43.203.228.137:32321\\",\\n\\"http:\\/\\/ttttt-1505394909.ap-northeast-2.elb.amazonaws.com\\"/g" ./visitor-service/api/main.py
                docker build -t ${DOCKER_IMAGE_OWNER}/frontendapp:v${env.Build_number} ./msa-frontend
                docker build -t ${DOCKER_IMAGE_OWNER}/adminapp:v${env.Build_number} ./admin-service
                docker build -t ${DOCKER_IMAGE_OWNER}/visitorapp:v${env.Build_number} ./visitor-service
                """
            }
        }
        
        stage('Docker Image Push') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-login', url: ""]) {                    
                    sh """
                    docker push ${DOCKER_IMAGE_OWNER}/frontendapp:v${env.Build_number}
                    docker push ${DOCKER_IMAGE_OWNER}/adminapp:v${env.Build_number}
                    docker push ${DOCKER_IMAGE_OWNER}/visitorapp:v${env.Build_number}
                    """
                }
            }
        }
        stage('Checkout2') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                    git clone https://github.com/bamyam/second-project-helm.git
                    ls second-project-helm
                    '''
                }
            }
        }
        stage('convert chart') {
            steps {
                sh """
                cd second-project-helm
                sed -i "s/v[0-9]\\+/v${env.Build_number}/g" msa-prd/values.yaml 
                """
            }
        }
        stage('second-project-helm push'){
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                sh '''
                cd /home/jenkins/workspace/second-project/second-project-helm
                git config --global user.email "seoseungjae20209@gmail.com"
                git config --global user.name "bamyam"
                git add msa-prd/values.yaml
                git commit -m "values commit"
                git push https://"${USERNAME}":"${PASSWORD}"@github.com/bamyam/second-project-helm.git HEAD:main
                '''
                }
            }
        }
    }
    
}

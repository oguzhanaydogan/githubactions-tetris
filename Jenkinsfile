pipeline{
    agent any
    stages{
        stage('git checkout'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/oguzhanaydogan/tetris-java-azure-devops']])
            }
        }
        stage('build Docker image'){
            steps{
                sh 'docker build -t oguzhanaydogan.azurecr.io/tetris .'
            }
        }
        stage('push image'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'acr', passwordVariable: 'password', usernameVariable: 'username')]) {
                sh 'docker login -u ${username} -p ${password} oguzhanaydogan.azurecr.io'        
                sh 'docker push oguzhanaydogan.azurecr.io/tetris'
                }
            }
        }
        stage('deploy web appp'){
            steps{
                withCredentials([azureServicePrincipal('azure_service_principal')]) {
                sh 'az login --service-principal -u ${AZURE_CLIENT_ID} -p ${AZURE_CLIENT_SECRET} --tenant ${AZURE_TENANT_ID}'
                }
                withCredentials([usernamePassword(credentialsId: 'acr', passwordVariable: 'password', usernameVariable: 'username')]) {
                sh 'az webapp config container set --name oguzhanaydogan-app --resource-group rg1 --docker-custom-image-name oguzhanaydogan.azurecr.io/tetris:latest --docker-registry-server-url https://oguzhanaydogan.azurecr.io --docker-registry-server-user ${username} --docker-registry-server-password ${password}'
                }
            }
        }
    }    
}

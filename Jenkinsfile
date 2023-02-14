pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        dockerhub=credentials('dockerhub')
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: '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', 
                url: 'https://github.com/somanaboina/cicd-end-to-end',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t somanaboina/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    echo '$dockerhub_PSW | docker login -u $dockerhub_USR **password-stdin'
                    docker push somanaboina/cicd-e2e:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: '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', 
                url: 'https://github.com/somanaboina/cicd-demo-manifests-repo.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: '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', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/somanaboina/cicd-demo-manifests-repo.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}

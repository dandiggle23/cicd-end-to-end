pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: '65803886-a586-414a-9707-046b576d66db', 
                url: 'https://github.com/dandiggle23/cicd-end-to-end',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t dante9623/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('push image') {
               withdockerregistry([ credentialsid: "b6582731-a465-4ea5-918f-ab32ab2d19b3", url: "https://index.docker.io/v1/" ]) {
               bat "dante9623/cicd-e2e:${BUILD_NUMBER}"
        }
        
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: '65803886-a586-414a-9707-046b576d66db', 
                url: 'https://github.com/dandiggle23/cicd-manifest-repo.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: '65803886-a586-414a-9707-046b576d66db', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/dandiggle23/cicd-manifest-repo.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
}
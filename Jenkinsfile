pipeline {
    agent any
    environment{
        staging_server="ec2-44-195-93-198.compute-1.amazonaws.com"
    }
    stages {
        stage("Verify tooling") {
            steps {
                sh '''
                    docker info
                    docker version
                    docker compose version
                '''
            }
        }
        stage("Verify SSH connection to server") {
            steps {
                sshagent(credentials: ['final-project']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ${staging_server} whoami
                    '''
                }
            }
        }        
        stage("Clear all running docker containers") {
            steps {
                script {
                    try {
                        sh 'docker rm -f $(docker ps -a -q)'
                    } catch (Exception e) {
                        echo 'No running container to clear up...'
                    }
                }
            }
        }
        stage("Start Docker") {
            steps {
                sh 'make up'
                sh 'docker compose ps'
            }
        }
        stage("Run Composer Install") {
            steps {
                sh 'docker compose run --rm composer update'
            }
        }
        stage("Populate .env file") {
            steps {
                dir("/var/lib/jenkins/workspace/envs/laravel-test") {
                    fileOperations([fileCopyOperation(excludes: '', flattenFiles: true, includes: '.env', targetLocation: "${WORKSPACE}")])
                }
            }
        }              
        stage("Run Tests") {
            steps {
                sh 'docker compose run --rm artisan key:generate && docker compose run --rm artisan test'
            }
        }
    }
    post {
        success {
            sh 'cd "/var/lib/jenkins/workspace/final-project"'
            sh 'rm -rf artifact.zip'
            sh 'zip -r artifact.zip . -x "*node_modules**"'
            withCredentials([sshUserPrivateKey(credentialsId: "final-project", keyFileVariable: 'keyfile')]) {
                sh 'scp -v -o StrictHostKeyChecking=no -i ${keyfile} /var/lib/jenkins/workspace/final-project/artifact.zip ${staging_server}:/home/ubuntu/artifact'
            }
            sshagent(credentials: ['final-project']) {
                sh 'ssh -o StrictHostKeyChecking=no ${staging_server} unzip -o /home/ubuntu/artifact/artifact.zip -d /var/www/html'
                script {
                    try {
                        sh 'ssh -o StrictHostKeyChecking=no ${staging_server} sudo chmod 777 /var/www/html/storage -R'
                    } catch (Exception e) {
                        echo 'Some file permissions could not be updated.'
                    }
                }
            }                                  
        }
        always {
            sh 'docker compose down --remove-orphans -v'
            sh 'docker compose ps'
        }
    }
}

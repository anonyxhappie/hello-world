ENVIRONMENTS = ['stag', 'prod']

pipeline {
    agent any

    tools {
      maven 'Maven'
    }
    
    parameters {
        string(name: 'BRANCH', defaultValue: 'master', description: 'Select branch to checkout')
        choice(name: 'ENVIRONMENT', choices: ENVIRONMENTS, description: 'Select environment to deploy the changes')
        booleanParam(name: 'executeTests', defaultValue: true, description: '')
    }
    
    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                sh 'mvn compile'
            }
        }
        stage('Test') {
            when {
                expression {
                    params.executeTests
                }
            }
            steps {
                echo 'Testing..'
                sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                sh "ssh dockeradmin@172.31.35.144 'rm -rf /home/dockeradmin/*'"
                sh "scp -r /var/lib/jenkins/workspace/temp-pipeline_dev/webapp/target/webapp.war /var/lib/jenkins/workspace/temp-pipeline_dev/Dockerfile dockeradmin@172.31.35.144:/home/dockeradmin"
                sh "ssh dockeradmin@172.31.35.144 'cd /home/dockeradmin && docker stop hello-world-container && docker rm hello-world-container && docker rmi hello-world-image && docker build -t hello-world-image . && docker run -d --name hello-world-container -p 8080:8080 hello-world-image; '"
            }
        }
    }
    post {
        success {
                echo 'SUCCESS!!!'
            emailext(
		        to: 'denowc@gmail.com',
		        subject: 'Release success', 
		        body: 'Everything went good',
		    )
        }
        failure {
                echo 'FAILED!!!'
                echo "Something is wrong with ${env.BUILD_URL}"
            emailext(
		        to: 'denowc@gmail.com',
		        subject: 'Release failed', 
		        body: 'Something went wrong',
		    )
        }
    }
}

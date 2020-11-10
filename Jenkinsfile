ENVIRONMENT = '172.31.35.144' //stag

if (env.BRANCH_NAME == 'master') {                                          
    ENVIRONMENT = '172.31.33.100'
}

pipeline {
    agent any

    tools {
      maven 'Maven'
    }
    
    parameters {
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
		sh "ssh dockeradmin@${ENVIRONMENT} 'rm -rf /home/dockeradmin/*'"
                sh "scp -r /var/lib/jenkins/workspace/temp-pipeline_dev/webapp/target/webapp.war /var/lib/jenkins/workspace/temp-pipeline_dev/Dockerfile dockeradmin@${ENVIRONMENT}:/home/dockeradmin"
                sh "ssh dockeradmin@${ENVIRONMENT} 'cd /home/dockeradmin && docker stop hello-world-container && docker rm hello-world-container && docker rmi hello-world-image && docker build -t hello-world-image . && docker run -d --name hello-world-container -p 8080:8080 hello-world-image; '"
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

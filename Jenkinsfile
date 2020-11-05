ENVIRONMENTS = ['stag', 'prod']

pipeline {
    agent any
    
    parameters {
        string(name: 'BRANCH', defaultValue: 'master', description: 'Select branch to checkout')
        choice(name: 'ENVIRONMENT', choices: ENVIRONMENTS, description: 'Select environment to deploy the changes')
        booleanParam(name: 'executeTests', defaultValue: true, description: '')
    }
    
    stages {
        stage('Build') {
            steps {
                echo 'Building..'
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
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
    post {
        success {
                echo 'SUCCUSS!!!'
        }
        failure {
                echo 'FAILED!!!'
                echo "Something is wrong with ${env.BUILD_URL}"
        }
    }
}

pipeline {
    agent { label 'python_agent'}

    stages {
        stage('First_stage') {
            steps {
                echo 'this is first stage'
                echo 'executtion triggered from github'
            }
        }
        
        stage('Second_stage') {
            steps {
                echo 'this is second stage'
            }
        }
        
        stage('third_stage') {
            steps {
                echo 'this is third stage'
            }
        }
    }
}

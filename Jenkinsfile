pipeline {
    agent {
        docker {
            image 'maven:3-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    environment {
        CI = 'true'
    }
    stages {
        stage('Pre-build') { 
            steps {
                sh './jenkins/scripts/pre-build.sh' 
            }
        }    
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver for development') { 
            when {
                branch 'development' 
            }
            steps {
                sh './jenkins/scripts/deliver.sh' 
            }
        }
        stage('Deploy for production') {
            when {
                branch 'production'  
            }
            steps {
                sh 'echo "pre deploy"'
                input message: 'Confirm deploy to production? (Click "Proceed" to continue)'
                sh './jenkins/scripts/deliver.sh' 
            }
        }
    }
}   
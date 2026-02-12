pipeline {
    agent any

    environment {
        DIL = "usermanagement-application-image:latest"
        DRN = "usermanagement"
    }

    stages {

        stage('Checkout') { steps { checkout scm } }
        stage('Maven Build') { steps { sh 'mvn -B clean package -DskipTests' } }
        stage('Docker Build') { steps { sh "docker build -t ${DIL} ."} }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials',
                usernameVariable: 'DH_USER',
                passwordVariable: 'DH_PASS')]) {
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        docker tag usermanagement-application-image:latest "$DH_USER"/usermanagement:latest
                        docker push "$DH_USER"/usermanagement:latest
                        docker logout
                    '''
                }
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker compose up -d'
                sh 'docker ps'
            }
        }
    }
}

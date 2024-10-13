pipeline {
    agent {label 'slave'}
    
    environment{
        SONAR_HOME = tool "Sonar"
    }
    
    parameters {
        string(name: 'FRONTEND_DOCKER_TAG', defaultValue: '', description: 'Setting docker image for latest push')
        string(name: 'BACKEND_DOCKER_TAG', defaultValue: '', description: 'Setting docker image for latest push')
    }
    
    stages {
        stage('Workspace cleanup') {
            steps {
                script {
                    cleanWs()
                }
            }
        }
        
        stage('Code Checkout') {
            steps {
                git url: "https://github.com/atkaridarshan04/wanderlust.git", branch: "main"
            }
        }
        
        stage('trivy file scan') {
            steps {
                sh "trivy fs --format table -o fs.html ."
                
            }
        }

        stage("OWASP: Dependency check"){
            steps{
                script{
                    dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'OWASP'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }
        
        stage('SonarQube: Code Analysis') {
            steps {
                withSonarQubeEnv("Sonar"){
                sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=wanderlust -Dsonar.projectKey=wanderlust -X"
                }
            }
        }
        
        stage("SonarQube: Code Quality Gates"){
            steps {
                timeout(time: 5, unit: "MINUTES"){
                waitForQualityGate abortPipeline: false
                }
            }
        }
        
        stage("Docker: Build Images") {
            steps {
                script {
                    sh '''
                    cd frontend
                    docker build -t atkaridarshan04/wanderlust-frontend:${FRONTEND_DOCKER_TAG} .
                    '''
                }
                script {
                    sh '''
                    cd backend
                    docker build -t atkaridarshan04/wanderlust-backend:${BACKEND_DOCKER_TAG} .
                    '''
                }   
            }
        }
        
        stage("Docker: Push images to DockerHub") {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'Docker', passwordVariable: 'dockerhubpass', usernameVariable: 'dockerhubuser')]) {
                    sh "docker login -u ${dockerhubuser} -p ${dockerhubpass}"
                    }
                    sh "docker push atkaridarshan04/wanderlust-frontend:${FRONTEND_DOCKER_TAG}"
                }
                script {
                    withCredentials([usernamePassword(credentialsId: 'Docker', passwordVariable: 'dockerhubpass', usernameVariable: 'dockerhubuser')]) {
                    sh "docker login -u ${dockerhubuser} -p ${dockerhubpass}"
                    }
                    sh "docker push atkaridarshan04/wanderlust-backend:${BACKEND_DOCKER_TAG}"
                }
            }
        }
    }

    post{
    success{
        archiveArtifacts artifacts: '*.xml', allowEmptyArchive: true, followSymlinks: false
        build job: "wanderlust-cd", parameters: [
            string(name: 'FRONTEND_DOCKER_TAG', value: "${params.FRONTEND_DOCKER_TAG}"),
            string(name: 'BACKEND_DOCKER_TAG', value: "${params.BACKEND_DOCKER_TAG}")
            ]
        }   
    }
}

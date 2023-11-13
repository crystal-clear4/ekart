pipeline {
    agent any
      tools {
        jdk  'jdk17'
        maven  'maven3'
    }
    
      environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
      stage('Git Checkout') {
            steps {
                git branch: 'main', credentialsId: 'df673a1e-3bea-4fa2-9498-03c0f9c26f79', url: 'https://github.com/crystal-clear4/ekart.git'            }
        }
      stage('COMPILE') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
          stage('OWASPScan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
          stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonar-server'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Ekart \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Ekart '''
                
            }
        }
    }
      stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
    
        stage('Docker Build & Push') {
            steps {
               script{
                   withDockerRegistry(credentialsId: '7f3c1923-f801-48b9-a326-fb29115c0d53', toolName: 'docker') {
                sh "docker build -t ekart -f docker/Dockerfile ."
                sh "docker tag ekart crystalclear123/ekart2:latest"
                sh "docker push crystalclear123/ekart2:latest"
}
               }
            }
        } 
        
        stage('Deploy') {
            steps {
               script{
                   withDockerRegistry(credentialsId: '7f3c1923-f801-48b9-a326-fb29115c0d53', toolName: 'docker') {
                    sh "docker run -d --name shop-shop4 -p 8050:8050 crystalclear123/ekart:latest"
                    
                    
}
               }
            }
        }   
    
}
}
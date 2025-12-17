pipeline {
    agent any
    
    tools {
        maven 'Maven'
        jdk 'JDK-17'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo "🔄 Checkout du code depuis Git"
                git branch: 'main', url: 'https://github.com/touta1234jh/devops.git'
            }
        }
        
        stage('Build') {
            steps {
                dir('timesheet-devops') {
                    echo "🛠️ Compilation du projet avec Maven (tests totalement ignorés)"
                    sh 'echo JAVA_HOME=$JAVA_HOME'
                    sh 'java -version'
                    sh 'mvn -version'
                    sh 'mvn clean package -Dmaven.test.skip=true'
                }
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                dir('timesheet-devops') {
                    echo "🔍 Analyse SonarQube"
                    script {
                        try {
                            withSonarQubeEnv('sonarqube') {
                                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                                    sh '''
                                        mvn sonar:sonar \
                                          -Dsonar.projectKey=MonProjet \
                                          -Dsonar.host.url=http://localhost:9000 \
                                          -Dsonar.login=$SONAR_TOKEN
                                    '''
                                }
                            }
                        } catch (Exception e) {
                            echo "⚠️ SonarQube analysis failed: ${e.message}"
                            echo "Continuing pipeline..."
                        }
                    }
                }
            }
        }
        
        stage('Archive') {
            steps {
                dir('timesheet-devops') {
                    echo "📦 Archivage des artefacts"
                    archiveArtifacts artifacts: 'target/*.jar,target/*.war', allowEmptyArchive: true
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
        always {
            echo '🔚 Pipeline terminé'
        }
    }
}
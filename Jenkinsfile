pipeline {
    agent any
    environment{
        SONAR_HOME = tool "Sonar"
    }

    stages {
        stage('Code Clone') {
            steps {
                git url: 'https://github.com/ajay-mall13/two-tier-flask-devops-robust-pipeline.git', branch: 'master'
            }
        }

        

    stage('GitLeaks Scan') {
    steps {
        sh '''
          gitleaks detect --source=. --report-path=gitleaks-report.json --report-format=json || true
        '''
    }
    post {
        always {
            archiveArtifacts artifacts: 'gitleaks-report.json', onlyIfSuccessful: false
        }
    }
}
        
        stage('sonar qube quality analysis'){
            steps{
                withSonarQubeEnv("Sonar"){
                    sh "$SONAR_HOME/bin/sonar-scanner  -Dsonar.projectName=portfolio -Dsonar.projectKey=portfolio"
                }
            }
        }
      
      
            stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./' , odcInstallation: 'dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'

                    }
        }
        
        
        stage("Trivy File System Scan") {
    steps {
        sh "trivy fs . -o results.json"
    }
}

stage("Sonar Quality Gate Scan") {
    steps {
        timeout(time: 2, unit: "MINUTES") {
            waitForQualityGate abortPipeline: false
        }
    }
}


        

        stage('Code Build') {
            steps {
                sh 'docker build -t my-app .'
            }
        }

        stage('test') {
            steps {
   		echo "devloper test dega"
            }
        }


    stage('Docker Build and Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerHubCreds',
                    usernameVariable: 'dockerHubUser',
                    passwordVariable: 'dockerHubPass'
                )]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker image tag my-app ${env.dockerHubUser}/two-tier-flask-app:latest"
                    sh "docker push ${env.dockerHubUser}/two-tier-flask-app:latest"
                }
            }
        }


        stage('Code Deploy') {
            steps {
                sh 'docker compose up -d --build flask-app'
            }
        }

    }
     post{
        success{
            script{
                emailext from: 'ajayup8082@gmail.com',
                to: 'ajayup8082@gmail.com',
                body: 'Build success for two-tier-flask-application',
                subject: 'Build success for two-tier-flask-application'
            }
        }
        failure{
            script{
                emailext from: 'ajayup8082@gmail.com',
                to: 'ajayup8082@gmail.com',
                body: 'Build Failed for two-tier-flask-application',
                subject: 'Build Failed for two-tier-flask-application'
            }
        }
    }
}
                                                                                                                                                                                           

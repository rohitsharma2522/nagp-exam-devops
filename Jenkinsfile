pipeline{
    agent any
    tools {
        maven "Maven3"
    }
    environment {
        registry="rohit2522/nagp-jenkins-assignment"
    }
    stages{
        stage("Checkout") {
            steps {
                echo "Checkout feature branch"
                checkout scm
            }
        }
        stage("Build") {
            steps {
                echo "Maven build"
                bat "mvn clean install"
            }
        }
        stage("Test") {
            steps {
                echo "Test cases"
                bat "mvn test"
            }
        }
        stage("Sonar Analysis") {
            steps {
                withSonarQubeEnv('Test_Sonar') {
                    "mvn sonar:sonar"
                }
            }
        }
        stage("Docker image") {
            steps {
                bat "docker build -t i-rohit2522-feature:${BUILD_NUMBER} --no-cache -f Dockerfile ."
            }
        }
        stage("Pre container check") {
            steps {
                bat "docker rm -f c-rohit2522-feature || exit 0"
            }
        }
        stage ("Docker tag") {
            steps {
                bat "docket tag i-rohit2522-feature:${BUILD_NUMBER} ${registry}:feature-latest"
                withDockerRegistry(credentialsId: 'Test_Docker', url:"") {
                    bat "docker push ${registry}:feature-latest"
                }
            }
        }
        stage("Deployment") {
            parallel {
                stage("Docker deployment"){
                    steps{
                        bat "docker run --name c-rohit2522-feature -d -p 7400:8080 ${registry}:feature-latest"
                    }
                }
                stage("Kubernetes Deployment"){
                    steps{
                        bat "kubectl apply -f deployment.yaml"
                    }
                }
            }
        }
    }
}
pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        dockerTool "docker"
        
    }

    stages {
        stage('VCS') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/harshhhhteen/hello-world-webapp.git'

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage('Deploy') {
            steps {
                script {
                    withDockerRegistry(
                        credentialsId: '9cc7a615-f974-4fab-9f48-1e585b7358cb',
                        toolName: 'docker') {
                        
                        // Build and Push
                        def echoServerImage = docker.build("harshhhhteen/hello-world-webapp:latest");
                        echoServerImage.push();
                    }
                }
            }
        }
    }
    post {
        // If Maven was able to run the tests, even if some of the test
        // failed, record the test results and archive the jar file.
        success {
            script {
            def msg = """
            {"text": ${GIT_COMMIT}}
            """;
            httpRequest url: "https://api.flock.com/hooks/sendMessage/d66ae8e5-24ae-4d15-bc82-182ef7e46982", contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: msg
            }
        }
        failure {
            echo "Failure! Duh!"
        }
    }
}

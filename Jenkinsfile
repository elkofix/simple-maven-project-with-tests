pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
    }

    parameters {
        choice(
            name: 'BRANCH',
            choices: ['declarative', 'testng'],
            description: 'Select the branch to build'
        )
    }

    stages {
        stage('Check and Clean Workspace') {
            steps {
                script {
                    // Define the directory of the project
                    def projectDir = "simple-maven-project-with-tests"

                    // Check if the directory exists
                    if (fileExists(projectDir)) {
                        echo "Project directory exists. Deleting..."
                        // Delete the directory
                        sh "rm -rf ${projectDir}"
                    } else {
                        echo "Project directory does not exist. Proceeding to clone..."
                    }
                }
            }
        }

        stage('Clone Repository') {
            steps {
                // Get some code from a GitHub repository, using the selected branch
                git branch: "${params.BRANCH}", url: 'https://github.com/jglick/simple-maven-project-with-tests.git'
            }
        }

        stage('Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
    }
}

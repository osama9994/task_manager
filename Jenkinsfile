pipeline {
    agent any

    environment {
        FLUTTER = "C:\\Users\\alsha\\scr\\flutter\\bin\\flutter.bat"
    }

    stages {

        stage('Jenkins Environment Info') {
            steps {
                echo "=================================================="
                echo "Job Name         : ${env.JOB_NAME}"
                echo "Build Number     : ${env.BUILD_NUMBER}"
                echo "Build Tag        : ${env.BUILD_TAG}"
                echo "Workspace Path   : ${env.WORKSPACE}"
                echo "Executing Node   : ${env.NODE_NAME}"
                echo "Executor ID      : ${env.EXECUTOR_NUMBER}"
                echo "Jenkins Home Path: ${env.JENKINS_HOME}"
                echo "Build URL        : ${env.BUILD_URL}"
                echo "=================================================="
            }
        }

        stage('Show User') {
            steps {
                bat 'whoami'
                bat 'echo USERPROFILE=%USERPROFILE%'
                bat 'echo APPDATA=%APPDATA%'
                bat 'echo TEMP=%TEMP%'
            }
        }

        stage('Flutter Version') {
            steps {
                bat '"%FLUTTER%" --version'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '"%FLUTTER%" pub get'
            }
        }

        stage('Analyze Code') {
            steps {
                bat '"%FLUTTER%" analyze'
            }
        }

        stage('Run Tests') {
            steps {
                bat '"%FLUTTER%" test'
            }
        }

        stage('Check Android Environment') {
            steps {
                bat 'echo ANDROID_HOME=%ANDROID_HOME%'
                bat 'echo ANDROID_SDK_ROOT=%ANDROID_SDK_ROOT%'
            }
        }

        stage('Flutter Doctor') {
            steps {
                bat '"%FLUTTER%" doctor -v'
            }
        }

        // stage('Build APK') {
        //     steps {
        //         bat '"%FLUTTER%" build apk --release'
        //     }
        // }
    }

    post {

        success {
            echo "SUCCESS: Job '${env.JOB_NAME}' (Build #${env.BUILD_NUMBER}) completed successfully."
            echo "Build URL: ${env.BUILD_URL}"
        }

        failure {
            echo "FAILURE: Job '${env.JOB_NAME}' (Build #${env.BUILD_NUMBER}) failed."
            echo "Check logs at: ${env.BUILD_URL}console"
        }

        always {
            echo "Pipeline execution finished for Build #${env.BUILD_NUMBER} on Node: ${env.NODE_NAME}."
        }
    }
}
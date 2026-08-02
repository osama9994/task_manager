pipeline {
    agent any

    environment {
        FLUTTER = "C:\\Users\\alsha\\scr\\flutter\\bin\\flutter.bat"
    }

    stages {

        stage('Flutter Version') {
            steps {
                bat "\"%FLUTTER%\" --version"
            }
        }

        stage('Install Dependencies') {
            steps {
                bat "\"%FLUTTER%\" pub get"
            }
        }

        stage('Analyze Code') {
            steps {
                bat "\"%FLUTTER%\" analyze"
            }
        }
        stage('Run Tests') {
    steps {
        bat "\"%FLUTTER%\" test"
    }
}
stage('Build APK') {
    steps {
        bat "\"%FLUTTER%\" build apk --release"
    }
}
    }
}
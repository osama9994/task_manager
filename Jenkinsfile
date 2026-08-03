pipeline {
    agent any

    environment {
        FLUTTER = "C:\\Users\\alsha\\scr\\flutter\\bin\\flutter.bat"
    }

    stages {
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
stage('Build APK') {
    steps {
        bat '"%FLUTTER%" build apk --release --verbose'
    }
}
    }
}
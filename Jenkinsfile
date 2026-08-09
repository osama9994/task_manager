pipeline {
agent any


environment {
    FLUTTER = "C:\\Users\\alsha\\scr\\flutter\\bin\\flutter.bat"
}

stages {

    stage('Environment Variables') {
        steps {
            bat 'echo JOB_NAME=%JOB_NAME%'
            bat 'echo BUILD_NUMBER=%BUILD_NUMBER%'
            bat 'echo BUILD_ID=%BUILD_ID%'
            bat 'echo WORKSPACE=%WORKSPACE%'
            bat 'echo USERPROFILE=%USERPROFILE%'
            bat 'echo PATH=%PATH%'
        }
    }

    stage('Check Branch Info') {
        steps {
            script {
                def branch = env.BRANCH_NAME ?: bat(
                    script: 'git name-rev --name-only HEAD',
                    returnStdout: true
                ).trim().split('\n').last()

                echo "=========================================="
                echo "Building for Branch: ${branch}"
                echo "=========================================="
            }
        }
    }

    stage('Install Dependencies') {
        when {
            expression {
                return env.GIT_CURRENT_BRANCH == 'main' ||
                       env.GIT_CURRENT_BRANCH == 'develop' ||
                       env.GIT_BRANCH?.contains('main')
            }
        }
        steps {
            bat '"%FLUTTER%" pub get'
        }
    }

    stage('Run Tests & Generate Reports') {
        when {
            expression {
                return env.GIT_CURRENT_BRANCH == 'main' ||
                       env.GIT_CURRENT_BRANCH == 'develop' ||
                       env.GIT_BRANCH?.contains('main')
            }
        }
        steps {
            bat '"%FLUTTER%" test --coverage --machine > test-results.json || exit 0'
            bat '"%FLUTTER%" pub run junitreport:tojunit --input test-results.json --output junit-report.xml'
        }
    }
}

post {
    always {
        script {
            if (fileExists('junit-report.xml')) {
                echo "Publishing Test Results & Coverage..."
                junit testResults: 'junit-report.xml', allowEmptyResults: true
                archiveArtifacts artifacts: 'coverage/lcov.info', allowEmptyArchive: true
                recordCoverage tools: [[parser: 'LCOV', pattern: 'coverage/lcov.info']]
            } else {
                echo "Skipped reporting: Not a targeted branch."
            }
        }
    }
}


}

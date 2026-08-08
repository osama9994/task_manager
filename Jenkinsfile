pipeline {
    agent any

    environment {
        FLUTTER = "C:\\Users\\alsha\\scr\\flutter\\bin\\flutter.bat"
    }

    stages {

        stage('Check Branch Info') {
            steps {
                script {
                    // استخراج اسم الفرع الحالي عبر Git
                    def currentBranch = bat(script: 'git rev-parse --abbrev-ref HEAD', returnStdout: true).trim().split('\n').last()
                    env.GIT_CURRENT_BRANCH = currentBranch
                    
                    echo "=========================================="
                    echo "Building for Branch: ${env.GIT_CURRENT_BRANCH}"
                    echo "=========================================="
                }
            }
        }

        stage('Install Dependencies') {
            when {
                expression { 
                    return env.GIT_CURRENT_BRANCH == 'main' || env.GIT_CURRENT_BRANCH == 'develop' || env.GIT_BRANCH?.contains('main')
                }
            }
            steps {
                bat '"%FLUTTER%" pub get'
            }
        }

        stage('Run Tests & Generate Reports') {
            when {
                expression { 
                    return env.GIT_CURRENT_BRANCH == 'main' || env.GIT_CURRENT_BRANCH == 'develop' || env.GIT_BRANCH?.contains('main')
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
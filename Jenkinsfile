pipeline {
    agent any

    parameters {
        string(
            name: 'APP_VERSION',
            defaultValue: '1.0.0',
            description: 'Application version'
        )

        choice(
            name: 'BUILD_TYPE',
            choices: ['debug', 'release'],
            description: 'Choose the Flutter build type'
        )

        booleanParam(
            name: 'RUN_TESTS',
            defaultValue: true,
            description: 'Run Flutter tests'
        )
    }

    environment {
        FLUTTER = "C:\\Users\\alsha\\scr\\flutter\\bin\\flutter.bat"
        APP_NAME = "task_manager"
    }

    stages {

        stage('Use Secret') {
            steps {
                withCredentials([
                    string(
                        credentialsId: 'TASK_MANAGER_SECRET',
                        variable: 'TASK_SECRET'
                    )
                ]) {
                    bat 'if defined TASK_SECRET (echo Secret is available) else (echo Secret is NOT available)'
                }
            }
        }

        stage('Environment Variables') {
            steps {
                echo "APP_VERSION = ${params.APP_VERSION}"
                echo "BUILD_TYPE = ${params.BUILD_TYPE}"
                echo "RUN_TESTS = ${params.RUN_TESTS}"
            }
        }
stage('Debug Configuration') {
    when {
        expression {
            return params.BUILD_TYPE == 'debug'
        }
    }
    steps {
        echo "Running DEBUG configuration"
        echo "App Version: ${params.APP_VERSION}"
    }
}

stage('Release Configuration') {
    when {
        expression {
            return params.BUILD_TYPE == 'release'
        }
    }
    steps {
        echo "Running RELEASE configuration"
        echo "App Version: ${params.APP_VERSION}"
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
                    return params.RUN_TESTS &&
                           (
                               env.GIT_CURRENT_BRANCH == 'main' ||
                               env.GIT_CURRENT_BRANCH == 'develop' ||
                               env.GIT_BRANCH?.contains('main')
                           )
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
                    recordCoverage tools: [[
                        parser: 'LCOV',
                        pattern: 'coverage/lcov.info'
                    ]]
                } else {
                    echo "Skipped reporting: Tests did not run."
                }
            }
        }
    }
}
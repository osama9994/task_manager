
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
                    bat '''
                        if not defined TASK_SECRET (
                            echo Secret is NOT available
                            exit /b 1
                        )

                        echo Secret is available

                        powershell -NoProfile -Command "$env:TASK_SECRET | Set-Content -Path 'android\\app\\google-services.json' -Encoding UTF8"

                        if exist "android\\app\\google-services.json" (
                            echo google-services.json created successfully
                        ) else (
                            echo Failed to create google-services.json
                            exit /b 1
                        )
                    '''
                }
            }
        }

        stage('Environment Variables') {
            steps {
                echo "=========================================="
                echo "Application Configuration"
                echo "=========================================="
                echo "APP_NAME    = ${env.APP_NAME}"
                echo "APP_VERSION = ${params.APP_VERSION}"
                echo "BUILD_TYPE  = ${params.BUILD_TYPE}"
                echo "RUN_TESTS   = ${params.RUN_TESTS}"
                echo "=========================================="
            }
        }

        stage('Debug Configuration') {
            when {
                expression {
                    return params.BUILD_TYPE == 'debug'
                }
            }

            steps {
                echo "=========================================="
                echo "Running DEBUG configuration"
                echo "App Version: ${params.APP_VERSION}"
                echo "=========================================="
            }
        }

        stage('Release Configuration') {
            when {
                expression {
                    return params.BUILD_TYPE == 'release'
                }
            }

            steps {
                echo "=========================================="
                echo "Running RELEASE configuration"
                echo "App Version: ${params.APP_VERSION}"
                echo "=========================================="
            }
        }

        stage('Check Branch Info') {
            steps {
                script {
                    def branch = env.BRANCH_NAME

                    if (!branch) {
                        branch = bat(
                            script: 'git name-rev --name-only HEAD',
                            returnStdout: true
                        ).trim().split('\n').last()
                    }

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
                echo "Installing Flutter dependencies..."

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
                echo "Running Flutter tests..."

                bat '"%FLUTTER%" test --coverage --machine > test-results.json || exit 0'

                echo "Generating JUnit report..."

                bat '"%FLUTTER%" pub run junitreport:tojunit --input test-results.json --output junit-report.xml'
            }
        }

        stage('Build Flutter APK - SIMULATED') {
            steps {
                script {

                    echo "=========================================="
                    echo "SIMULATED FLUTTER BUILD"
                    echo "=========================================="

                    if (params.BUILD_TYPE == 'release') {

                        echo "Build Type : RELEASE"
                        echo "App Name   : ${env.APP_NAME}"
                        echo "Version    : ${params.APP_VERSION}"

                        echo "Simulating:"
                        echo "flutter build apk --release"

                        echo "Expected APK:"
                        echo "build\\app\\outputs\\flutter-apk\\app-release.apk"

                    } else {

                        echo "Build Type : DEBUG"
                        echo "App Name   : ${env.APP_NAME}"
                        echo "Version    : ${params.APP_VERSION}"

                        echo "Simulating:"
                        echo "flutter build apk --debug"

                        echo "Expected APK:"
                        echo "build\\app\\outputs\\flutter-apk\\app-debug.apk"
                    }

                    echo "=========================================="
                    echo "BUILD SIMULATION SUCCESS"
                    echo "No real Gradle build was executed."
                    echo "=========================================="
                }
            }
        }

        stage('Verify APK - SIMULATED') {
            steps {
                script {

                    def apkPath

                    if (params.BUILD_TYPE == 'release') {
                        apkPath = 'build\\app\\outputs\\flutter-apk\\app-release.apk'
                    } else {
                        apkPath = 'build\\app\\outputs\\flutter-apk\\app-debug.apk'
                    }

                    echo "=========================================="
                    echo "APK Verification"
                    echo "=========================================="
                    echo "Expected APK:"
                    echo "${apkPath}"
                    echo ""
                    echo "APK exists: SIMULATED"
                    echo "APK verification: SUCCESS"
                    echo "=========================================="
                }
            }
        }
    }

    post {
        always {
            script {

                if (fileExists('junit-report.xml')) {

                    echo "Publishing Test Results & Coverage..."

                    junit(
                        testResults: 'junit-report.xml',
                        allowEmptyResults: true
                    )

                    archiveArtifacts(
                        artifacts: 'coverage/lcov.info',
                        allowEmptyArchive: true
                    )

                    recordCoverage(
                        tools: [[
                            parser: 'LCOV',
                            pattern: 'coverage/lcov.info'
                        ]]
                    )

                } else {

                    echo "Skipped reporting: Tests did not run."
                }
            }
        }
    }
}


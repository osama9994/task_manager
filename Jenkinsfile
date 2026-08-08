pipeline {
    agent any

    environment {
        FLUTTER = "C:\\Users\\alsha\\scr\\flutter\\bin\\flutter.bat"
    }

    stages {

        stage('Check Branch Info') {
            steps {
                script {
                    echo "=========================================="
                    echo "Building for Branch: ${env.BRANCH_NAME}"
                    echo "=========================================="
                }
            }
        }

        stage('Install Dependencies') {
            // الشرط: ينفذ فقط إذا كان الفرع main أو develop
            when {
                anyOf {
                    branch 'main'
                    branch 'develop'
                    // يمكنك إضافة فروع أخرى مثل: branch 'feature/*'
                }
            }
            steps {
                bat '"%FLUTTER%" pub get'
            }
        }

        stage('Run Tests & Generate Reports') {
            // شرط الفروع لضمان عدم تشغيل الاختبارات على الفروع غير المسموحة
            when {
                anyOf {
                    branch 'main'
                    branch 'develop'
                }
            }
            steps {
                // 1. تشغيل الاختبارات وتوليد التقرير
                bat '"%FLUTTER%" test --coverage --machine > test-results.json || exit 0'

                // 2. تحويل التقرير إلى صيغة JUnit XML
                bat '"%FLUTTER%" pub run junitreport:tojunit --input test-results.json --output junit-report.xml'
            }
        }
    }

    post {
        always {
            script {
                // التقارير تُنشر فقط إذا تم إنشاء الملفات فعلياً (أي في الفروع المسموحة)
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
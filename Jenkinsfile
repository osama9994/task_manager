pipeline {
    agent any

    environment {
        FLUTTER = "C:\\Users\\alsha\\scr\\flutter\\bin\\flutter.bat"
    }

    stages {

        stage('Install Dependencies') {
            steps {
                bat '"%FLUTTER%" pub get'
            }
        }

        stage('Run Tests & Generate Reports') {
            steps {
                // إضافة --coverage لتوليد ملف coverage/lcov.info
                bat '"%FLUTTER%" test --coverage --machine > test-results.json || exit 0'

                // تحويل التقرير إلى JUnit XML
                bat '"%FLUTTER%" pub run junitreport:tojunit --input test-results.json --output junit-report.xml'
            }
        }
    }

    post {
        always {
            echo "Publishing Test Results & Coverage..."

            // 1. نشر تقارير الاختبارات
            junit testResults: 'junit-report.xml', allowEmptyResults: true

            // 2. أرشفة ملف التغطية (سيصبح موجوداً الآن)
            archiveArtifacts artifacts: 'coverage/lcov.info', allowEmptyArchive: true

            // 3. نشر التغطية الرسومية
            recordCoverage tools: [[parser: 'LCOV', pattern: 'coverage/lcov.info']]
        }
    }
}
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

        stage('Run Tests & Generate JUnit Report') {
            steps {
                // 1. تشغيل الاختبارات واستخراج النتيجة بصيغة JSON
                // استخدام || exit 0 يضمن استمرار البناء لتوليد التقرير حتى لو فشل اختبار
                bat '"%FLUTTER%" test --machine > test-results.json || exit 0'

                // 2. تحويل ملف JSON إلى تقرير JUnit XML
                bat '"%FLUTTER%" pub run junitreport:tojunit --input test-results.json --output junit-report.xml'
            }
        }
    }

    post {

        always {
            echo "Publishing Test Results..."

            // أرشفة ملف الـ XML الأصلي
            archiveArtifacts artifacts: 'junit-report.xml', allowEmptyArchive: true

            // أمر Jenkins المدمج لتحليل تقرير JUnit وعرض الرسوم البيانية
            junit testResults: 'junit-report.xml', allowEmptyResults: true
        }

        success {
            echo "SUCCESS: All tests passed and report published."
        }

        failure {
            echo "FAILURE: Pipeline execution failed."
        }
    }
}

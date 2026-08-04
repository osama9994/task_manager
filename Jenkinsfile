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
// pipeline {
//     agent any

//     environment {
//         FLUTTER = "C:\\Users\\alsha\\scr\\flutter\\bin\\flutter.bat"
//     }

//     stages {

//         stage('Jenkins Environment Info') {
//             steps {
//                 echo "=================================================="
//                 echo "Job Name         : ${env.JOB_NAME}"
//                 echo "Build Number     : ${env.BUILD_NUMBER}"
//                 echo "Build Tag        : ${env.BUILD_TAG}"
//                 echo "Workspace Path   : ${env.WORKSPACE}"
//                 echo "Executing Node   : ${env.NODE_NAME}"
//                 echo "Build URL        : ${env.BUILD_URL}"
//                 echo "=================================================="
//             }
//         }

//         stage('Install Dependencies') {
//             steps {
//                 bat '"%FLUTTER%" pub get'
//             }
//         }

//         stage('Analyze Code') {
//             steps {
//                 // حفظ نتيجة التحليل في ملف نصي لأرشفته لاحقاً
//                 bat '"%FLUTTER%" analyze > analyze-report.txt'
//             }
//         }

//         stage('Run Tests') {
//             steps {
//                 // تشغيل الاختبارات وحفظ التقرير التفصيلي
//                 bat '"%FLUTTER%" test --reporter expanded > test-report.txt'
//             }
//         }
//     }

//     post {

//         always {
//             echo "Pipeline execution finished for Build #${env.BUILD_NUMBER}."
            
//             // أرشفة التقارير الناتجة لتظهر في صفحة البناء في Jenkins
//             archiveArtifacts artifacts: '*.txt', allowEmptyArchive: true
//         }

//         success {
//             echo "SUCCESS: Job '${env.JOB_NAME}' (Build #${env.BUILD_NUMBER}) completed successfully."
//         }

//         failure {
//             echo "FAILURE: Job '${env.JOB_NAME}' (Build #${env.BUILD_NUMBER}) failed."
//         }
//     }
// }
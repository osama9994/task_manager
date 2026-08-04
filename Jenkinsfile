pipeline {
    agent any

    environment {
        FLUTTER = "C:\\Users\\alsha\\scr\\flutter\\bin\\flutter.bat"
    }

    stages {

        stage('Jenkins Environment Info') {
            steps {
                echo "=================================================="
                echo "Job Name         : ${env.JOB_NAME}"
                echo "Build Number     : ${env.BUILD_NUMBER}"
                echo "Build Tag        : ${env.BUILD_TAG}"
                echo "Workspace Path   : ${env.WORKSPACE}"
                echo "Executing Node   : ${env.NODE_NAME}"
                echo "Build URL        : ${env.BUILD_URL}"
                echo "=================================================="
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '"%FLUTTER%" pub get'
            }
        }

        stage('Analyze Code') {
            steps {
                // حفظ نتيجة التحليل في ملف نصي لأرشفته لاحقاً
                bat '"%FLUTTER%" analyze > analyze-report.txt'
            }
        }

        stage('Run Tests') {
            steps {
                // تشغيل الاختبارات وحفظ التقرير التفصيلي
                bat '"%FLUTTER%" test --reporter expanded > test-report.txt'
            }
        }
    }

    post {

        always {
            echo "Pipeline execution finished for Build #${env.BUILD_NUMBER}."
            
            // أرشفة التقارير الناتجة لتظهر في صفحة البناء في Jenkins
            archiveArtifacts artifacts: '*.txt', allowEmptyArchive: true
        }

        success {
            echo "SUCCESS: Job '${env.JOB_NAME}' (Build #${env.BUILD_NUMBER}) completed successfully."
        }

        failure {
            echo "FAILURE: Job '${env.JOB_NAME}' (Build #${env.BUILD_NUMBER}) failed."
        }
    }
}
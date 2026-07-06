pipeline {
    agent any

    triggers {
        // runs every 5 minutes
        cron('*/5 * * * *')
    }

    environment {
        PYTHONUNBUFFERED = '1'
        // job name of the scraper pipeline to trigger
        JENKINS_JOB_NAME = 'scraper/job/trend-scraper'
    }

    stages {
        stage('Setup') {
            steps {
                sh '''
                    if [ ! -d ".venv" ]; then
                        python3 -m venv .venv
                    fi
                    .venv/bin/pip install -q -r requirements.txt
                '''
            }
        }

        stage('Run scheduler') {
            steps {
                withCredentials([
                    string(credentialsId: 'supabase-url',        variable: 'SUPABASE_URL'),
                    string(credentialsId: 'supabase-secret-key', variable: 'SUPABASE_SECRET_KEY'),
                    string(credentialsId: 'jenkins_url',         variable: 'JENKINS_URL'),
                    string(credentialsId: 'jenkins_user',        variable: 'JENKINS_USER'),
                    string(credentialsId: 'jenkins_api_token',   variable: 'JENKINS_API_TOKEN'),
                ]) {
                    sh '.venv/bin/python scheduler.py'
                }
            }
        }
    }

    post {
        failure {
            echo "Scheduler tick failed"
        }
    }
}

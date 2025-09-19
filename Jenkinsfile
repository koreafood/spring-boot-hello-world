pipeline {
    agent any
    
    tools {
        maven 'Maven-3.9.0' // Jenkins에서 설정된 Maven 버전
        jdk 'JDK-17' // Jenkins에서 설정된 JDK 버전
    }
    
    environment {
        MAVEN_OPTS = '-Dmaven.test.failure.ignore=true'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building the application...'
                sh 'mvn clean compile'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
            post {
                always {
                    // 테스트 결과 발행
                    publishTestResults testResultsPattern: 'target/surefire-reports/*.xml'
                    // 테스트 커버리지 보고서 (JaCoCo 사용시)
                    // publishCoverage adapters: [jacocoAdapter('target/site/jacoco/jacoco.xml')]
                }
            }
        }
        
        stage('Package') {
            steps {
                echo 'Packaging the application...'
                sh 'mvn package -DskipTests'
            }
            post {
                success {
                    // JAR 파일 아카이브
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
        
        stage('Code Quality Analysis') {
            steps {
                echo 'Running code quality analysis...'
                // SonarQube 분석 (SonarQube 서버가 설정된 경우)
                // withSonarQubeEnv('SonarQube') {
                //     sh 'mvn sonar:sonar'
                // }
                
                // 간단한 정적 분석
                sh 'mvn checkstyle:check || true'
                sh 'mvn pmd:check || true'
            }
        }
        
        stage('Deploy to Staging') {
            when {
                branch 'develop'
            }
            steps {
                echo 'Deploying to staging environment...'
                // 스테이징 환경 배포 스크립트
                sh '''
                    echo "Stopping existing application..."
                    pkill -f "spring-boot" || true
                    sleep 5
                    
                    echo "Starting new application..."
                    nohup java -jar target/*.jar --server.port=8081 > staging.log 2>&1 &
                    sleep 10
                    
                    echo "Health check..."
                    curl -f http://localhost:8081/api/hello || exit 1
                '''
            }
        }
        
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying to production environment...'
                // 프로덕션 배포는 수동 승인 필요
                input message: 'Deploy to production?', ok: 'Deploy',
                      submitterParameter: 'DEPLOYER'
                
                sh '''
                    echo "Deploying to production..."
                    # 실제 프로덕션 배포 스크립트
                    # docker build -t hello-world:${BUILD_NUMBER} .
                    # docker push your-registry/hello-world:${BUILD_NUMBER}
                    # kubectl set image deployment/hello-world hello-world=your-registry/hello-world:${BUILD_NUMBER}
                '''
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed.'
            // 워크스페이스 정리
            cleanWs()
        }
        success {
            echo 'Pipeline succeeded!'
            // 성공 알림 (Slack, 이메일 등)
            // slackSend channel: '#ci-cd', 
            //           color: 'good', 
            //           message: "✅ Build succeeded: ${env.JOB_NAME} - ${env.BUILD_NUMBER}"
        }
        failure {
            echo 'Pipeline failed!'
            // 실패 알림
            // slackSend channel: '#ci-cd', 
            //           color: 'danger', 
            //           message: "❌ Build failed: ${env.JOB_NAME} - ${env.BUILD_NUMBER}"
        }
        unstable {
            echo 'Pipeline is unstable.'
        }
    }
}
// Declarative //
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building..'
                sh 'cd webdemo && ./gradlew build -x integTest -x test'
            }

        }

        stage('Test') {
            steps {
                echo 'Testing..'
                sh 'cd webdemo && ./gradlew build -x integTest'

            }
        }

        
        
        stage('SonarQube analysis') {
           steps {
               echo "starting codeAnalyze with SonarQube......"
               script{
               def sonarqubeScannerHome = tool name:'SonarScannerTest'
               withSonarQubeEnv('SonarSeverTest') {
                   sh "${sonarqubeScannerHome}/bin/sonar-scanner"
               }
            
               timeout(4) { 
                   //利用sonar webhook功能通知pipeline代码检测结果，未通过质量阈，pipeline将会fail
                   def qg = waitForQualityGate() 
                       if (qg.status != 'OK') {
                           error "未通过Sonarqube的代码质量阈检查，请及时修改！failure: ${qg.status}"
                       }
                   }
               }
           }


       }
        
        stage('Deploy') {
          //  when {
             //   expression {
                    /*如果测试失败，状态为UNSTABLE*/
               //     currentBuild.result == 'SUCCESS'
            //   }
         //   }
            steps {
                echo 'Deploying..'
         
                //sh 'ssh -tt hbao@10.209.21.215 < deploy.sh'

                sh """
                set -e
                ssh bywang@10.209.21.159 'bash -s' < checktomcatstatus.sh


                cd /var/jenkins_home/workspace/TestForPipeline/webdemo/build/libs

                scp webdemo.war bywang@10.209.21.159:/Users/hbao/Downloads/apache-tomcat-7.0.82/webapps
                ssh hbao@10.209.21.215 '
                cd /Users/hbao/Downloads/apache-tomcat-7.0.82/bin
                ./startup.sh
                '
                """

            }
        }

            }
        }




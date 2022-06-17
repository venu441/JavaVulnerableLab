pipeline {
  agent any 
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
    stage ('Check-Git-Secrets') {
      steps {
        sh ' rm trufflehog || true '
        sh ' docker run -t gesellix/trufflehog --json https://github.com/abhinav1144/JavaVulnerableLab.git> trufflehog '
        sh ' cat trufflehog '
      }
    }
    
     stage ('Build') {
      steps {
        sh 'sudo mvn clean install'
      }
    }
     stage ('SAST') {
      steps {
        sh 'sudo mvn clean install sonar:sonar -Dsonar.host.url=http://54.92.218.102:9000 -Dsonar.login=admin -Dsonar.password=admin123 -Dsonar.projectName=JavaVurnability'
      }
    }
     stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['ee3c29f7-1033-459f-a644-0d3c22982d78']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@18.234.236.86:/home/ubuntu/project/apache-tomcat-9.0.62/webapps/VulnerableJavaWebApplication.war'
              }      
           }       
    }
    
    
    stage ('DAST') {
      steps {
        sshagent(['ee3c29f7-1033-459f-a644-0d3c22982d78']) {
         sh 'ssh -o  StrictHostKeyChecking=no ubuntu@18.234.236.86 "sudo docker run -t owasp/zap2docker-stable zap-baseline.py -t http://18.234.236.86:8080/VulnerableJavaWebApplication/" || true'
        }
      }
    }
	  
  }
}

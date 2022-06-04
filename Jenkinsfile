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
    stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/abhinav1144/JavaVulnerableLab/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      }
    }
     stage ('Build') {
      steps {
        sh 'sudo mvn clean install'
      }
    }
     stage ('SAST') {
      steps {
        sh 'sudo mvn clean install sonar:sonar -Dsonar.host.url=http://54.221.95.105:9000 -Dsonar.login=admin -Dsonar.password=admin123 -Dsonar.projectName=JavaVurnability'
      }
    }
     stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['ee3c29f7-1033-459f-a644-0d3c22982d78']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@54.226.71.32:/home/ubuntu/project/apache-tomcat-9.0.62/webapps/VulnerableJavaWebApplication.war'
              }      
           }       
    }
    
    
    stage ('DAST') {
      steps {
        sshagent(['ee3c29f7-1033-459f-a644-0d3c22982d78']) {
         sh 'ssh -o  StrictHostKeyChecking=no ubuntu@54.226.71.32 "sudo docker run -t owasp/zap2docker-stable zap-baseline.py -t http://54.226.71.32:8080/VulnerableJavaWebApplication/" || true'
        }
      }
    }
	  
  }
}

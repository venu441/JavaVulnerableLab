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
     stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn clean install -u'
          sh 'cat target/sonar/report-task.txt'
        }
      }
    }
	  
  }
}

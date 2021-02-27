import groovy.json.JsonOutput
import groovy.transform.Field
import groovy.json.JsonSlurper

node ('master') 
{
  try {
    scannerHome = tool 'sonar'
  }
}
Checkout()   //  cloning 
sonarqube() // code quality check 
quality_gate // check the quality gate and failed the job
Build()      // mvn clean install
deploying_to_tomcat() 
}
def Checkout() 
{
    stage('Checkout')
    {
    
        checkout scm
        
    }
def sonarqube()
{
stage ("sonarqube") 
{
withSonarQubeEnv ('sonar')
{
sh " cd ${workspace}/sample ; ${scannerHome}/bin/sonar-scanner -e -Dsonar.projectName=sample -Dsonar.projectKey=sample -Dsonar.source=${workspace}/sample -Dsonar.java.binaries=target/class"

}

}



}

def quality_gate()
{
stage("quality_gate")
{
  sleep "10"
timeout(time: 1, unit: 'HOURS')
{
  def qg = waitForQualityGate()
  if (qg.status == 'ERROR')
  {
     error "pipeline aborted due to quality gate failure: $(qg.status)"

  }
}
}

}
def Build() 
{
    stage('Build') 
    {
       sh "cd ${workspace}/sample ; mvn clean install " 
    }
}

def deploying_to_tomcat() {
      
stage('deploying_to_tomcat') {
      node ("ansible-machine")
      {
            sh "cd /opt ; sudo ansible-playbook devops.yaml"
            
      }
}


}


    

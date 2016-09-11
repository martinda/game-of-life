@Library("github.com/pwolfbees/pipeline") _

pipeline{
   agent docker:'pwolf/cjptower'

   stages {
      stage('Build'){
         sh "mvn clean package"
      }
      stage ('Archive'){
         archiveArtifacts artifacts: 'gameoflife-web/target/gameoflife.war', excludes: null, onlyIfSuccessful: true
      }
      stage("Deploy"){
         withTower(host:"https://104.198.10.204", credentials:"tower-cli"){
         sh 'tower-cli job launch --job-template=31'
         }
      }   
   }
}

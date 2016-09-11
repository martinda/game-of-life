@Library("github.com/pwolf/pipeline") _

node ('linux') {
   stage('Build'){
    checkout scm
    def mvnHome = tool 'M3.0.5-linux'
    sh "${mvnHome}/bin/mvn clean install"
   }
   
   stage ('Archive'){
    archive 'gameoflife-web\\target\\gameoflife.war'
   }
   
    stage("Deploy"){
        withTower(host:"https://104.198.10.204", credentials:"tower-cli"){
        sh 'tower-cli job launch --job-template=31'
        }
    }   
}

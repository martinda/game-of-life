@Library("github.com/pwolfbees/pipeline") _

pipeline{
   agent docker:'pwolf/cjptower'

   stages {
      stage('Build'){
         sh "mvn clean package"
         checkpoint "Commit and Package"
      }
      stage ('Archive'){
         step([$class: 'S3BucketPublisher', dontWaitForConcurrentBuildCompletion: false, 
            entries: [[bucket: 'cjptower', excludedFile: '', flatten: false, gzipFiles: false, keepForever: false, 
            managedArtifacts: false, noUploadOnFailure: true, selectedRegion: 'us-west-2', showDirectlyInBrowser: false, 
            sourceFile: 'gameoflife-web/target/gameoflife.war', storageClass: 'STANDARD', uploadFromSlave: true, useServerSideEncryption: false]], 
            profileName: 'cjp-tower-demo', userMetadata: []])
      }
      stage("Deploy"){
         withTower(host:"https://104.198.10.204", credentials:"tower-cli"){
         sh 'tower-cli job launch --job-template=31'
         }
      }   
   }
}

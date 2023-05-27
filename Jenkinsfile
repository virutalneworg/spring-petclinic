pipeline {
    agent { label 'build_node' }
    stages {
        stage ('Git clone') {
            steps {
                git url: 'https://github.com/virtualram-rgb/spring-petclinic.git', branch: 'main'
            }
        }
        stage('artifactory'){
            steps{
                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER" ,
                    serverId: 'jfrog_instance',
                    releaseRepo: 'libs-release-local',
                    snapshotRepo: 'libs-snapshot-local',
                    deployArtifacts: 'true' 
                )
            }
        }
        stage ('build') {
            steps {
                withSonarQubeEnv('SONAR_SELF_HOSTED'){
                    sh 'mvn install sonar:sonar'
                }
            }
        }
        stage('Deploy to Artifactory') {
            steps {
                // Use the rtMavenRun step to deploy artifacts to Artifactory
                rtMavenRun(
                    tool: 'mvn_2',
                    deployerId: 'MAVEN_DEPLOYER',
                    pom: 'pom.xml',
                    goals: 'deploy'
                )
            }
        }
        stage('Publishtheartifacts'){
            steps{
                rtPublishBuildInfo (
                    serverId: 'jfrog_instance'
                )
            }
        }
    }
}

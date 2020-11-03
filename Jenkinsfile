node{
    def buildNumber = BUILD_NUMBER
    stage("Git CheckOut"){
        git url: 'https://github.com/helloranirobin/MavenWebApp.git',branch: 'master'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "Maven-3.6.1", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
    } 
    
    stage("Build Docker Image") {
         sh "docker build -t rani12345/maven-web-app:${buildNumber} ."
    }
    
    stage("Docker Push"){
        withCredentials([string(credentialsId: 'Docker_Hub_Pwd', variable: 'Docker_Hub_Pwd')]) {
          sh "docker login -u rani12345 -p ${Docker_Hub_Pwd}"
        }
        sh "docker push rani12345/maven-web-app:${buildNumber}"
    }
    
    stage("Deploy to Kubernetes Cluster"){
        sshagent(['Docker_Swarm_Manager_Dev']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.47.232 docker service rm javawebapp || true'
            sh "ssh ubuntu@172.31.47.232 docker service create --name javawebapp -p 8080:8080 --replicas 2 dockerhandson/java-web-app:${buildNumber}"
        }
    }
}
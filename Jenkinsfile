pipeline {
    
	agent any
	
	tools {
        maven "Maven3"
        jdk "OracleJDK11"
    }
	
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'seun'
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "172.20.4.149:8081"
        RELEASE_REPO = "thedevcloud-mvn-hosted-repo"
        CENTRAL_REPO = "thedevcloud-proxy-repo"
	    NEXUS_GRP_REPO    = "thedevcloud-group-repo"
        NEXUS_LOGIN = "nexuslogin"
        ARTVERSION = "${env.BUILD_ID}"
    }
	
    stages{
        
        stage('BUILD'){
            steps {
                sh 'mvn -s settings.xml clean install -DskipTests'
            }
        }   
    }
}
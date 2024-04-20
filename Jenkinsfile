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
        NEXUSIP = "172.31.46.214"
        NEXUSPORT = '8081'
        RELEASE_REPO = "thedev-repo-1"
        CENTRAL_REPO = "thedevcloud-proxy-repo"
	    NEXUS_GRP_REPO    = "thedevcloud-repo-grp"
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
pipeline {
	agent any

//	tools {
//		jdk 'jdk8'
//	}
//	environment {
//		M2_INSTALL = "/usr/bin/mvn"
//	}

    stages {
        stage('Clone-Repo') {
	    steps {
	        checkout scm
	    }
        }

        stage('Build') {
            steps {
                sh 'mvn install -Dmaven.test.skip=true'
            }
        }
		
        stage('Unit Tests') {
            steps {
                sh 'mvn compiler:testCompile'
                sh 'mvn surefire:test'
                junit 'target/**/*.xml'
            }
        }
        stage('SonarQube Analysis') {
         def mvn = tool 'Default Maven';
         withSonarQubeEnv() {
                sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=Pipe -Dsonar.projectName='Pipe'"
	 }
	}


        stage('Deployment') {
            steps {
                sh 'sshpass -p "tomcat" scp target/gamutgurus.war tomcat@172.17.0.2:/home/tomcat/apache-tomcat-8.5.87/webapps'
                sh 'sshpass -p "tomcat" ssh tomcat@172.17.0.2 "/home/tomcat/apache-tomcat-8.5.87/bin/shutdown.sh"'
		sh 'sshpass -p "tomcat" ssh tomcat@172.17.0.2 "/home/tomcat/apache-tomcat-8.5.87/bin/startup.sh"'
            }
        }
    }
}

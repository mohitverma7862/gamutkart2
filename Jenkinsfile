pipeline {
	agent any
	
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
	    stage('Sonar') {
		    steps {
			    sh "mvn sonar:sonar -Dsonar.host.url=http://http://172.17.0.2:9000 -Dsonar.login=$SONAR"
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

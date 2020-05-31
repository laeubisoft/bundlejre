pipeline {
    agent any
    tools { 
        jdk 'JDK8' 
        maven 'MAVEN3'
    }
    triggers {
        pollSCM('@daily')
    }
    options {
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '1'))
    }
    stages {
    	stage('checkout') {
    	steps {
    			dir('bundlejre') {
					checkout scm
					//windows 64
					sh 'wget -nv -N https://cdn.azul.com/zulu/bin/zulu11.39.15-ca-fx-jre11.0.7-win_x64.zip'
					sh 'unzip -o zulu8.33.0.1-ca-fx-jdk8.0.192-win_x64.zip -d openchrom/features/net.openchrom.jre.win32.win32.x86_64.feature/jre'
					//linux x86_64
					sh 'wget -nv -N https://cdn.azul.com/zulu/bin/zulu11.39.15-ca-fx-jre11.0.7-linux_x64.tar.gz'
					sh 'tar -xvzf zulu8.33.0.1-ca-fx-jdk8.0.192-linux_x64.tar.gz -C openchrom/features/net.openchrom.jre.linux.gtk.x86_64.feature/jre'
					//mac osx x86_64
					sh 'wget -nv -N https://cdn.azul.com/zulu/bin/zulu11.39.15-ca-fx-jre11.0.7-macosx_x64.tar.gz'
					sh 'tar -xvzf zulu8.33.0.1-ca-fx-jdk8.0.192-macosx_x64.tar.gz -C openchrom/features/net.openchrom.jre.macosx.cocoa.x86_64.feature/jre'
				}
			}
    	}
		stage('package') {
			steps {
				sh 'mvn -B -Dmaven.repo.local=.repository -f bundlejre/openchrom/features/pom.xml install'
				sh 'mvn -B -Dmaven.repo.local=.repository -f bundlejre/openchrom/sites/pom.xml install'
			}
		}
		stage('deploy') {
			when { branch 'develop' }
			steps {
				withCredentials([string(credentialsId: 'JRE_DEPLOY_HOST', variable: 'JRE_DEPLOY_HOST'), string(credentialsId: 'JRE_DEPLOY_PATH', variable: 'JRE_DEPLOY_PATH')]) {
					sh 'scp -r bundlejre/openchrom/sites/net.openchrom.jre.updatesite/target/site/* '+"${JRE_DEPLOY_HOST}${JRE_DEPLOY_PATH}${BRANCH_NAME}"
				}
			}
		}
    }
    post {
        success {
            cleanWs notFailBuild: true
        }

    }
}
pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "MAVEN_HOME"
    }

    stages {
        stage('Clone') {
            steps {
                timeout(time: 2, unit: 'MINUTES'){
                    git branch: 'main', credentialsId: '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', url: 'https://github.com/dmamanipar/PSD2025-IG1-Proyecto.git'
                }
            }
        }
        stage('Build') {
            steps {
                timeout(time: 8, unit: 'MINUTES'){
                    sh "mvn -DskipTests clean package -f SysAlmacen/pom.xml"
                }
            }
        }
        stage('Test') {
            steps {
                timeout(time: 10, unit: 'MINUTES'){
                    // Se cambia <test> por <install> para que se genere el reporte de jacoco
                    sh "mvn clean install -f SysAlmacen/pom.xml"
                }
            }
        }
        stage('Sonar') {
            steps {
                timeout(time: 4, unit: 'MINUTES'){
                    withSonarQubeEnv('sonarqube'){
                        sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.0.2155:sonar -Pcoverage -f SysAlmacen/pom.xml"
                    }
                }
            }
        }
        stage('Quality gate') {
            steps {

                sleep(10) //seconds

                timeout(time: 4, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }
		stage('Deploy to Tomcat') {
			 steps {
			 timeout(time: 5, unit: 'MINUTES') {
			 script {
			 // Ruta del archivo WAR generado
			 def warFile = "SysAlmacen/target/SysAlmacen.war"
			 
			 // Subir el WAR usando curl (Tomcat Manager API)
			 sh """
			 curl -u admin:s3cret -T "${warFile}" http://172.22.1.8:8080/manager/text/deploy?path=/SysAlmacen&update=true
			 """
			 }
			 }
			}
		}

		
		
		
		
    }
}
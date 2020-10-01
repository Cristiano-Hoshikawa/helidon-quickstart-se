pipeline {
    agent any
    tools {
      maven 'Maven-cris' 
      'org.jenkinsci.plugins.docker.commons.tools.DockerTool' 'Docker-cris'
    }   
    /*
    environment {
        registry = "iad.ocir.io/idavixsf5sbx/cristianohoshikawa"
        registryCredential = 'docker-credential'
        app = ''
    }
    */
    stages {
        stage('Build') { 
            steps {
                sh "mvn -f pom.xml package" 
            }
        }
        stage('Create docker image') { 
            steps {
                script {
                    def scmVars = checkout([
                        $class: 'GitSCM',
                        doGenerateSubmoduleConfigurations: false,
                        userRemoteConfigs: [[
                            url: 'https://github.com/Cristiano-Hoshikawa/helidon-quickstart-se.git'
                          ]],
                        branches: [ [name: '*/master'] ]
                      ])
                    /* app = docker.build(registry + "/helidon-quickstart-se:latest") */
                    sh 'docker build -t iad.ocir.io/' + params.DOCKER_REPO + '/helidon-quickstart-se:latest --file /Dockefile .'
                }
            }
        }
        stage('Push image to OCIR') { 
            steps {
                script {
                    def scmVars = checkout([
                        $class: 'GitSCM',
                        doGenerateSubmoduleConfigurations: false,
                        userRemoteConfigs: [[
                            url: 'https://github.com/Cristiano-Hoshikawa/helidon-quickstart-se.git'
                          ]],
                        branches: [ [name: '*/master'] ]
                      ])
    /*
                            docker.withRegistry('https://iad.ocir.io', 'docker-credential') {
                            app.push(registry + "/helidon-quickstart-se")
                        }               
    */
                    sh 'docker login https://iad.ocir.io -u ' + params.REGISTRY_USERNAME + ' -p "' + params.REGISTRY_TOKEN + '"'
                    sh 'docker push iad.ocir.io/' + params.DOCKER_REPO + '/helidon-quickstart-se:latest'
                }                       
            }
        }
        
        stage('Remove Unused docker image') {
          steps{
            sh "docker rmi iad.ocir.io/" + params.DOCKER_REPO + "/helidon-quickstart-se:latest"
          }
        } 
    }
}

pipeline {
    agent { label 'slave' }
    environment {
        SCRIPT_GIT_URL = 'https://github.com/sahilkaushik007/jenkins.git'
    }
    
    stages {
        stage('Checkout') {
            steps {
                script{
                FIREWALL_RULES_GIT_URL = ""
                if (params.Environment == 'prod') {
                    FIREWALL_RULES_GIT_URL = 'https://github.com/sahilkaushik007/jenkins.git'
                } 
                else {
                    FIREWALL_RULES_GIT_URL = 'https://github.com/sahilkaushik007/jenkins.git'
                }

                echo 'Cloning repo..'
                checkout([  
                         $class: 'GitSCM', 
                         branches: [[name: 'master']], 
                         doGenerateSubmoduleConfigurations: false, 
                         extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'manual-rules-config']], 
                         submoduleCfg: [], 
                         userRemoteConfigs: [[credentialsId: 'suricata-git-user', url: "${FIREWALL_RULES_GIT_URL}"]]
                        ])

                checkout([  
                         $class: 'GitSCM', 
                         branches: [[name: 'master']], 
                         doGenerateSubmoduleConfigurations: false, 
                         extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'execution-script']], 
                         submoduleCfg: [], 
                         userRemoteConfigs: [[credentialsId: 'suricata-git-user', url: "${SCRIPT_GIT_URL}"]]
                        ])
                        sh"""
                           mv ${WORKSPACE}/manual-rules-config/Firewall-configs/manual_pass.json ${WORKSPACE}/execution-script/egress-traffic-control/suricata-rule-kreator
                        """

                }
            }
        }
        stage('Dependency Installations') {
            steps {
                script{
                    sh """
                       cd  ${WORKSPACE}/execution-script/egress-traffic-control/suricata-rule-kreator
                       pip3 install -r requirements.txt --no-cache-dir
                    """
                }
            }
        }
        stage('Script Execution') {
            steps {
                script{
                    try {
                      sh """
                       cd  ${WORKSPACE}/execution-script/egress-traffic-control/suricata-rule-kreator
                       python3 manual_rules.py
                      """

                    }
                    catch (Exception e) {
                      echo 'Err: Script Execution failed with Error: ' + e.toString()              
                   }     
                }
            }
        }
    }
    post { 
        always { 
            echo 'GoodBye!..'
            cleanWs()
        }
    }
}

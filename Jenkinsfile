pipeline{

    agent any
    parameters{
		string(name: 'deployTo', defaultValue: '', description: 'Deployment environment? e.g TEST/UAT')
    }
    environment{
        testServer = '192.168.1.6'
        uatServer = '192.168.1.7'
    }
    stages{
        stage('Deploy container in QA server'){
            when{
				environment name: 'deployTo', value: 'TEST'
			}
			steps{
				script{
					sh '''
                    new_tag=$(cat /tmp/gitTag)
                    cont_ID=$(ssh jnsadmin@$testServer 'docker ps -qa --filter name=samplewebapp')
                    if [[ -z "$cont_ID" ]];
                    then
                        docker -H ssh://jnsadmin@$testServer run --name samplewebapp  -d -p 8000:8080 santoshgoswami/samplewebapp:$new_tag       
					else
						ssh jnsadmin@$testServer docker rm "${cont_ID}" -f
						docker -H ssh://jnsadmin@$testServer run --name samplewebapp  -d -p 8000:8080 santoshgoswami/samplewebapp:$new_tag       
                    
                    fi
					'''
					def ver=readFile('/tmp/gitTag').trim()
                    def msg=readFile('/tmp/commitID').trim()
                    
                    subject= "FDA - Build # ${currentBuild.number} - QA Deployment Successful"
                    body="<html><body>Hi Santosh,<br><br>FDA Container has been deployed on QA environment.<br>URL: http://$testServer:8000/LoginWebApp-1/<br>${msg}<br><br>Regards,<br>DevOps Team.</body></html>"
                    mail_to="santoshgoswami691@gmail.com"
                    mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"
                    
                    subject= "FDA - Build # ${currentBuild.number} -- QA Revert Request"
                    body= "<html><head><meta http-equiv=Content-Type content=text/html; charset=utf-8><style>.button{background-color:red;border-color:red;border:2px solid red;padding:10px;text-align:center;}.link{display:block;color:#ffffff;font-size:12px;text-decoration:none; text-transform:uppercase;}</style></head><body><br>In order to revert to previous deployment click on below button.<br><br>Current Deployed FDA Container Version : ${ver}<br><br><table><tr><td class=button><a class=link href=#>Revert</a></td><td></td><td></td></tr></table></body></html>"
                    mail_to="santoshgoswami691@gmail.com"
                    mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"
                    
                    subject= "FDA - Build # ${currentBuild.number} -- UAT Deployment Request"
                    body= "<html><head><meta http-equiv=Content-Type content=text/html; charset=utf-8><style>.button{background-color:green;border-color:green;border:2px solid green;padding:10px;text-align:center;}.link{display:block;color:#ffffff;font-size:12px;text-decoration:none; text-transform:uppercase;}</style></head><body><br>In order to deploy FDA container on UAT environment click on below button.<br><br>FDA Container Version: ${ver}<br><br><table><tr><td class=button><a class=link href=http://192.168.1.5:8080/view/TestAppDouble7/job/Double7_QA-UAT/buildWithParameters?deployTo=UAT&token=abc>Approve</a></td><td></td><td></td></tr></table></body></html>"
                    mail_to="santoshgoswami691@gmail.com"
                    mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"
                            
				}
			}
			post{
				failure{
					script{
						currentBuild.result = "FAILED"
						subject= "FDA - Build # ${currentBuild.number} -- Frontend Build ${currentBuild.result}!"
						body="<html><head></head><body>Hi all,<br><br>Some problem occurred while Deploying Docker container to QA server.<br><br>Regards,<br>DevOps Team.</body></html>"
						mail_to="santoshgoswami691@gmail.com"
						mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"
						
					}
				}
			}
		}
		
		stage('Deploy container in UAT server'){
            when{
				environment name: 'deployTo', value: 'UAT'
			}
			steps{
				script{
					sh '''
                    new_tag=$(cat /tmp/gitTag)
                    cont_ID=$(ssh jnsadmin@$uatServer 'docker ps -qa --filter name=samplewebapp')
                    if [[ -z "$cont_ID" ]];
                    then
                        docker -H ssh://jnsadmin@$uatServer run --name samplewebapp  -d -p 8000:8080 santoshgoswami/samplewebapp:$new_tag       
					else
						ssh jnsadmin@$uatServer docker rm "${cont_ID}" -f
						docker -H ssh://jnsadmin@$uatServer run --name samplewebapp  -d -p 8000:8080 santoshgoswami/samplewebapp:$new_tag       
                    
                    fi
					'''
					def ver=readFile('/tmp/gitTag').trim()
                    def msg=readFile('/tmp/commitID').trim()
                    
                    subject= "FDA - Build # ${currentBuild.number} - UAT Deployment Successful"
                    body="<html><body>Hi all,<br><br>FDA Container has been deployed on UAT environment.<br>URL: http://$uatServer:8000/LoginWebApp-1/<br>${msg}<br><br>Regards,<br>DevOps Team.</body></html>"
                    mail_to="santoshgoswami691@gmail.com"
                    mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"
                    
                    subject= "FDA - Build # ${currentBuild.number} -- UAT Revert Request"
                    body= "<html><head><meta http-equiv=Content-Type content=text/html; charset=utf-8><style>.button{background-color:red;border-color:red;border:2px solid red;padding:10px;text-align:center;}.link{display:block;color:#ffffff;font-size:12px;text-decoration:none; text-transform:uppercase;}</style></head><body><br>In order to revert to previous deployment click on below button.<br><br>Current Deployed FDA Container Version : ${ver}<br><br><table><tr><td class=button><a class=link href=#>Revert</a></td><td></td><td></td></tr></table></body></html>"
                    mail_to="santoshgoswami691@gmail.com"
                    mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"        
				}
			}
			post{
				failure{
					script{
						currentBuild.result = "FAILED"
						subject= "FDA - Build # ${currentBuild.number} -- Frontend Build ${currentBuild.result}!"
						body="<html><head></head><body>Hi all,<br><br>Some problem occurred while Deploying Docker container to UAT server.<br><br>Regards,<br>DevOps Team.</body></html>"
						mail_to="santoshgoswami691@gmail.com"
						mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"
						
					}
				}
			}
		}
		
		
    }
}

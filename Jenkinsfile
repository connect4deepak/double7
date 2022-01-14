pipeline{

    agent any
    environment{
        testServer = '192.168.1.7'
    }
    stages{
        stage('Deploy container in Remote server'){
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
				}
			}
			post{
				failure{
					script{
						currentBuild.result = "FAILED"
						subject= "FDA - Build # ${currentBuild.number} -- Frontend Build ${currentBuild.result}!"
						body="<html><head></head><body>Hi all,<br><br>Some problem occurred while Deploying Docker container to Dev server.<br><br>Regards,<br>DevOps Team.</body></html>"
						mail_to="santoshgoswami691@gmail.com"
						mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"
						
					}
				}
			}
		}
        stage('Deployment Status'){
			steps{
				script{
					echo "Checking url:- http://$testServer:8000/LoginWebApp-1/"
					sleep 30
					withCredentials([string(credentialsId: 'githubAccessToken', variable: 'githubAccessToken')]) {
                        sh '''
                        new_tag=$(cat /tmp/gitTag)
                        status=$(curl -so /dev/null -w '%{response_code}' http://$testServer:8000/LoginWebApp-1/) || true
                        if [[ "$status" -eq 200 ]]
                        then
							echo "Deployment Successfull"
                            echo 'SUCCESS' > /tmp/deployment_status
                            git tag "$new_tag"
                            git push https://goswami97:"$githubAccessToken"@github.com/goswami97/double7.git --tags
                        else
                            echo "Deployment Fail"
                            echo 'FAIL' > /tmp/deployment_status
                        fi
                        '''
                    }
					def output=readFile('/tmp/deployment_status').trim()
					if( "$output" == "FAIL"){
						echo "Deployment Fail, Reverting to previos build"
						sh '''
						current_build=`cat /tmp/gitTag`
                        major=$(echo "$current_build" | awk -F "." '{print $1}')
                        minor=$(echo "$current_build" | awk -F "." '{print $2}')
                        patch=$(echo "$current_build" | awk -F "." '{print $3}')
                        oldpatch=$(expr $patch - 1)
						previous_build="${major}.${minor}.${oldpatch}"
        
                        echo "Previous tag $previous_build"

                        cont_ID=$(ssh jnsadmin@$testServer 'docker ps -qa --filter name=samplewebapp')
                        ssh jnsadmin@$testServer docker rm "${cont_ID}" -f
                        docker -H ssh://jnsadmin@$testServer run --name samplewebapp  -d -p 8000:8080 santoshgoswami/samplewebapp:$previous_build                   
						'''
					}else{
						echo "Deployment Success"
						
						def ver=readFile('/tmp/gitTag').trim()
						def msg=readFile('/tmp/commitID').trim()
						
						subject= "FDA - Build # ${currentBuild.number} - QA Deployment Successful"
                        body="<html><body>Hi all,<br><br>FDA Container has been deployed on QA environment.<br>URL: http://$testServer:8000/LoginWebApp-1/<br>${msg}<br><br>Regards,<br>DevOps Team.</body></html>"
                       	mail_to="santoshgoswami691@gmail.com"
                        mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"

                        subject= "FDA - Build # ${currentBuild.number} -- QA Revert Request"
                        body= "<html><head><meta http-equiv=Content-Type content=text/html; charset=utf-8><style>.button{background-color:red;border-color:red;border:2px solid red;padding:10px;text-align:center;}.link{display:block;color:#ffffff;font-size:12px;text-decoration:none; text-transform:uppercase;}</style></head><body><br>In order to revert to previous deployment click on below button.<br><br>Current Deployed FDA Container Version : ${ver}<br><br><table><tr><td class=button><a class=link href=#>Revert</a></td><td></td><td></td></tr></table></body></html>"
                        mail_to="santoshgoswami691@gmail.com"
                        mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"
                                                            
                        subject= "FDA - Build # ${currentBuild.number} -- UAT Deployment Request"
                        body= "<html><head><meta http-equiv=Content-Type content=text/html; charset=utf-8><style>.button{background-color:green;border-color:green;border:2px solid green;padding:10px;text-align:center;}.link{display:block;color:#ffffff;font-size:12px;text-decoration:none; text-transform:uppercase;}</style></head><body><br>In order to deploy FDA container on UAT environment click on below button.<br><br>FDA Container Version: ${ver}<br><br><table><tr><td class=button><a class=link href=http://192.168.1.5:8080/view/TestAppDouble7/job/Double7_UAT/build?token=abc>Approve</a></td><td></td><td></td></tr></table></body></html>"
                        mail_to="santoshgoswami691@gmail.com"
                        mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"
                            
                        echo 'Mail Sent'
                        
					}
				}
			}
			post{
				failure{
					script{
						currentBuild.result = "FAILED"
						subject= "FDA - Build # ${currentBuild.number} -- Frontend Build ${currentBuild.result}!"
						body="<html><head></head><body>Hi all,<br><br>Some problem occurred while checking deployment status for DEV environment.<br><br>Regards,<br>DevOps Team.</body></html>"
						mail_to="santoshgoswami691@gmail.com"
						mail bcc: '', body: "${body}", cc: '', charset: 'UTF-8', from: '',mimeType: 'text/html', replyTo: '', subject: "${subject}", to: "${mail_to}"
							
					}
				}
			}
		}



    }
}

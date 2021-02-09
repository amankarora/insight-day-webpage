pipeline {
    agent any
    stages {
      stage('Setup parameters') {
        steps {
          script { 
            properties([
              parameters([
                string(
                    defaultValue: '34.245.91.68', 
                    name: 'NGINX_DEV_IP', 
                    trim: true
                ),
                string(
                    defaultValue: '52.210.134.244', 
                    name: 'NGINX_PROD_IP', 
                    trim: true
                ),
                string(
                    defaultValue: 'insight-day-key', 
                    name: 'insight_day_key', 
                    trim: true
                )
              ])
            ])
          }
        }
      }
      stage('Code Checkout') {
        steps {
          checkout scm
        }
      }

	    stage ('Deploy dev') {
	      steps {
          sshagent(credentials: [params.insight_day_key]) {
            sh """
            rsync -e "ssh -o StrictHostKeyChecking=no" -r $WORKSPACE/sites/ ubutu@$NGINX_DEV_IP:/usr/share/nginx/html/
            """
	        }
        }
      }

      stage ('Test Dev') {
	      steps {
          sh '''
            RESPONSE_CODE=\$(curl -o /dev/null -s -w "%{http_code}\n" http://$NGINX_DEV_IP)
            if [ "$RESPONSE_CODE" != 200 ]; then
                echo curl unsuccessful.  Expected response code 200, got "$RESPONSE_CODE"
            fi
            '''
	       }
      }

	    stage ('Deploy prod') {
	      steps {
          sshagent(credentials: [params.insight_day_key]) {
            sh """
            rsync -e "ssh -o StrictHostKeyChecking=no" -r $WORKSPACE/sites/ ubuntu@$NGINX_DEV_IP:/usr/share/nginx/html/
            """
	        }
	      }
	    }
    }
}

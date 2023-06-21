pipeline {
    agent any

	environment {
		MYSQL_ROOT_PASSWORD = credentials('DB_PASSWORD')	
	}
	
    stages {
        stage('cleanup') {
            steps {
                sh '''
                if docker logs nginx; then
                    if docker exec nginx ls; then
                        docker stop nginx
		            else
			            sleep 1
                    fi
                    docker rm nginx
		        else
		            sleep 1
                fi
                if docker logs flask-app; then
                    if docker exec flask-app ls; then
                        docker stop flask-app
		            else
			            sleep 1
                    fi
		docker rm flask-app
		else
		sleep 1
		fi
   		if docker logs mysql; then
                    if docker exec mysql ls; then
                        docker stop mysql
		            else
			            sleep 1
                    fi
		docker rm mysql
		else
		sleep 1
		fi
  docker rmi trio-db:v1
  docker rmi trio-app:v1
               '''
            }
        }
        stage('Build') {
            steps {
                sh '''
		cd flask-app
		docker build -t trio-app:v1 .
  		cd ../db
   		docker build -t trio-db:v1 .
   		'''
            }
        }
        stage('run') {
            steps {
                sh '''
                docker network inspect trio-net && sleep 1 || docker network create trio-net
                docker run -d --network trio-net --name mysql -e MYSQL_ROOT_PASSWORD=${password123} trio-db:v1 
                docker run -d --network trio-net --name flask-app -e MYSQL_ROOT_PASSWORD=${password123} trio-app:v1
		docker run -d -p 80:80 --network trio-net --mount type=bind,source=$(pwd)/nginx/nginx.conf,target=/etc/nginx/nginx.conf --name nginx nginx:alpine
                '''
            }
        }
    }
}

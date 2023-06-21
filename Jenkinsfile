pipeline {
    agent any
      
    stages {
        stage('checkout source code') {
            steps {
                git url: "https://gitlab.com/qacdevops/chaperootodo_client", branch: "master"
            }
        }
        stage('cleanup') {
            steps {
                sh '''
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
               '''
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t trio-db:v1 .'
            }
        }
        stage('run') {
            steps {
                sh '''
               docker network inspect trio-net && sleep 1 || docker network create trio-net
                docker run -d --network trio-net -e MYSQL_ROOT_PASSWORD=password123 --name mysql trio-db:v1
                docker run -d -p 80:5000 --network trio-net -e MYSQL_ROOT_PASSWORD=password123 --name flask-app trio-task:v2

                '''
            }
        }
    }
}

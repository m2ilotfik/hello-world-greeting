pipeline {

  

  agent none

  

  stages {

    

    stage('Compilation et tests') {



      agent {

        label 'agent_java'

      }



      stages {

  

        stage('Test unitaire & publication') {

    

          steps {

            sh 'mvn test'

          }

      

          post {

      

            always {        

              junit 'target/surefire-reports/*.xml'

            }

        

          }

      

        }



        stage('Analyse statique') {

      

          steps {

        

           

              sh 'echo "test"'

        


          }

      

        }



        stage('Compilation') {

    

          steps {  

            sh 'mvn -B -DskipTests clean package'

          }

      

        }

            

        stage('Publication du binaire') {



          steps {
                                                                     
            sh "curl -u admin:lotfi --upload-file target/*.war 'http://10.10.20.31:8081/repository/depot_test/rondoudou${BUILD_NUMBER}.war'"        

          }



        }

    

      }

  

    }

    

    stage('Tests de déploiement') {

      

      agent {

        label 'agent_tomcat'

      }

      

      stages {

        

        stage('Téléchargement du binaire') {

          

          steps {

            sh "wget --user=admin --password=lotfi -P /home/jenkins/tomcat/webapps http://10.10.20.31:8081/repository/depot_test/rondoudou${BUILD_NUMBER}.war"

            sh "mv /home/jenkins/tomcat/webapps/rondoudou${BUILD_NUMBER}.war /home/jenkins/tomcat/webapps/rondoudou.war"

          }

 

        }

        

        stage('Test de performance') {

          

          steps {

            sh '/home/jenkins/apache-jmeter/bin/jmeter.sh -n -t ./jmeter.jmx -l /home/jenkins/test_report.jtl'

          }

         

        }

        

        stage ('Validation de l\'application') {

  

          steps {

    

            sh "curl -u admin:lotfi --upload-file /home/jenkins/tomcat/webapps/rondoudou.war 'http://10.10.20.31:8081/repository/hello_fiable/rondoudou_fiable${BUILD_NUMBER}.war'"

            sh "curl -u admin:lotfi --upload-file /home/jenkins/tomcat/webapps/rondoudou.war 'http://10.10.20.31:8081/repository/hello_livrable/dernier_rondoudou_fiable.war'"

  

          }

  

        }

        

      }

      

    }

    

    stage('Creation de l\'image') {

      

      agent {

        label 'agent_docker'

      }


          


      stages {
        
          stage ('tesst') {
              steps {
                sh 'echo "test" '
              }
            }
        

        stage('Téléchargement du binaire') {

          

          steps {

            sh 'wget --user=admin --password=lotfi -P /home/jenkins/docker/tomcat_app http://10.10.20.31:8081/repository/hello_livrable/dernier_rondoudou_fiable.war'

            sh 'mv /home/jenkins/docker/tomcat_app/dernier_rondoudou_fiable.war /home/jenkins/docker/tomcat_app/rondoudou.war'

          }

          

        }

          

        stage('Compilation de l\'image') {

      

          steps {
            sh 'apt-get update'
            sh 'apt-get install docker.io -y'
         
            sh 'docker build -t tomcat_app /home/jenkins/docker/tomcat_app'

          }

          

        }

        

        stage('Stockage de l\'image') {

              

          steps {

            sh "docker tag tomcat_app reeban/tomcat_app:${BUILD_NUMBER}"

            sh 'docker tag tomcat_app reeban/tomcat_app'

            sh 'docker login -u matrix1233 -p 123456123456'

            sh "docker push reeban/tomcat_app:${BUILD_NUMBER}"

            sh 'docker push reeban/tomcat_app'

          }

         

        }

      }

    }

  }

}

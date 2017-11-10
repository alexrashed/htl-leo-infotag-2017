# Continuous Delivery with Docker & LiFX

- Make sure `docker` and `docker-compose` are installed
- Update the .env file
- Create the needed docker network and volume in advance:
```
$ docker network create htl-infotag
$ docker volume create htl-infotag-gogs
```
- Fire up the compose (make sure to allow sharing the local volume folders with docker):
```
$ docker-compose up -d
```
- Configure gogs:
  - Go to [http://localhost:3000/](http://localhost:3000/) and set the mysql host to `gogs-db:3306` and set the password as defined in `.env`. Also configure an admin account.
  - Create a new repository (f.e. `htl-infotag-helloworld`)
  - Add a new webhook to jenkins for the repository (f.e. `http://jenkins:8080/gogs-webhook/?job=htl-infotag`, select a secret, only send pushes and use json)
- Configure jenkins:
  - Go to [http://localhost:8080/](http://localhost:8080/)
  - Set up Jenkins using the admin token:
  ```
  $ docker-compose exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
  ```
  - Install the recommended plugins
  - After the normal setup login and install these additional plugins:
    - Gogs plugin
    - Notificatoin plugin
  - Create a new job as represented by the [job_config.xml](config/jenkins/job_config.xml) file (or import it using an API token and the REST API).
- Configure the `helloworld` project:
  - Add the new repository as an upstream to the helloworld project and push it:
    ```
    git init
    git add -A
    git commit -m "Initial commit"
    git remote add origin http://localhost:3000/<username>/<repo-name>
    git push -u origin master
    ```
- Watch the continuous delivery chain:
  1. gogs calls jenkins
  2. jenkins pushes to the registry
  3. jenkins notifies the buildbulb
  4. the registry calls the redeployer
  5. the redeployer calls `config/webhook/redeploy.sh` in the `compose/product` folder

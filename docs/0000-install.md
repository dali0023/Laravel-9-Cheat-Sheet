### Install With Sail(Docker)

wsl:
user: dali0023
pass: BR0455815

1. search `wsl` on windows and double it to run. it will run internally.
2. Open Docker `Desktop>settings>Resources>WSL Integration`:
   - Enable integration with my default WSL distro
   - Ubuntu

![Docker](./../resources/img/install.png)

3. open `CMD Terminal` as `Run As Administor` mode, then type: `wsl --install -d Ubuntu`
4. go to: [Laravel Sail](https://laravel.com/docs/10.x/installation#choosing-your-sail-services)
5. Run on terminal: `curl -s "https://laravel.build/example-app?with=mysql,redis" | bash`
6. it will ask pass: BR0455815
7. ls
8. `cd `test and run terminal to open apps on VS Code: `code .`

10. `./vendor/bin/sail up` & then we will see our apps to Docker Desktop and aslo visit localhost.

visit: http://localhost

10. Adding PHPMyAdmin to Laravel Sail:
Go to Project `docker-compose.yml`:
```yml
    phpmyadmin:
        depends_on:
            - mysql
        image: phpmyadmin/phpmyadmin
        environment:
            - PMA_HOST=mysql
            - PMA_PORT=3306
        networks:
            - sail
        ports:
            - 8080:80
networks:
    sail:
        driver: bridge
```
11. go to: `http://localhost:8080/`
check user an password from project `.env file`
user name: sail
password: password

you will see all of your databases!

##### Testing:
run from sail in docker: `./vendor/bin/sail test`

##### Create Test:
```
./vendor/bin/sail artisan make:test UserTest
./vendor/bin/sail artisan make:test MyTest --unit
```

## Launch an instance AWS Linux Server:
* **Name and tags**: any name (ex: my-aws-server)
* **Application and OS Images (Amazon Machine Image):** Amazon Linux AWS
* **Instance type:** select default (any free tier eligible)
* **Key pair (login):** Create new key pair:
     * Key pair name (any name ex: aws-web-server-key)
     * Key pair type (RSA)
     * Private key file format: 
          * for use with OpenSSH select: .pem
          * for use with **Putty** select: **.ppk**
          * create and download key to use for putty
* **Network settings:** 
     * Create security group
     * select all : 
          - [x] Allow SSH traffic from, 
          - [x] Allow HTTPS traffic from the internet, 
          - [x] Allow HTTP traffic from the internet
* **Launch Instance**

## Install MobaXterm or Putty to connect Amazon Linux Server to local machine.
* **For Putty:**
   * Copy Public IPv4 address(ex: 35.182.74.38) from Amazon EC2 Server.
   * Host Name: IPv4 address and Default port: 22 
   * In the Category pane, expand ```Connection```, expand ```SSH```, and then select ```Auth```. Complete the following:
       * select Private key file for Authentication
           * Select the **.ppk file (aws-web-server-key)** that you generated for your key pair.
       * then Open
       * Login as: ec2-user

* **For MobaXterm:**
   * Copy Public IPv4 address(ex: 35.182.74.38) from Amazon EC2 Server
   * MobaXterm>Session>SSH>
   * Remote Host- 35.182.74.38>Select Specify Username- any name(ex: ec2-user)
   * Advanced SSh Settings: select Use Private key- upload Key pair file that we download when create EC2 Server.
   * Ok
   * New linux terminal will open, to go root dir> run: sudo su –
   * Pwd to check

## Docker Install on Amazon Linux
* **Install Docker**
```sh
sudo yum update -y
sudo yum -y install docker
```
* **Start Docker**
```sh 
sudo service docker start
```
* **Access Docker commands in ec2-user user**
```sh
sudo usermod -a -G docker ec2-user
sudo chmod 666 /var/run/docker.sock
docker version
```

Make docker auto-start

`sudo chkconfig docker on`

Because you always need it....

`sudo yum install -y git`

Reboot to verify it all loads fine on its own.

`sudo reboot`

## docker-compose install

Copy the appropriate `docker-compose` binary from GitHub:

`sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose`

NOTE: to get the latest version (thanks @spodnet):
`sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose`

Fix permissions after download: 

`sudo chmod +x /usr/local/bin/docker-compose`

Verify success: 

`docker-compose version`




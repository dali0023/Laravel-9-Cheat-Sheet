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

## Launch an instance:
* **Name and tags**: any name (ex: my-aws-server)
* **Application and OS Images (Amazon Machine Image):** Amazon Linux AWS
* **Instance type:** select default (any free tier eligible)
* **Key pair (login):** Create new key pair:
     * Key pair name (any name ex: aws-webserverkey)
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

Amazon changed the install in Linux 2. One no-longer using 'yum'
See: https://aws.amazon.com/amazon-linux-2/release-notes/

## Docker CE Install

```sh
sudo amazon-linux-extras install docker
sudo service docker start
sudo usermod -a -G docker ec2-user
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




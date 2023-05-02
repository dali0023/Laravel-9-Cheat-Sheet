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







How to run drupal related modules test cases (Unit/Functional/Intergration).
-------------

Summary :-

    To run test cases in drupal module we need build drupal core package and drupal complete framework which include one database,apache server,and drupal core package.
    There are 3 type of tests in drupal unit,functional,intergration. For unit test we dont need drupal full framework like database.
    Unit test does not use database testing as i tested some packages.

*************************

Copy following files From local into VM thats needed to run docker file succesfully.

    1) automate_drupal.sh.txt
    2) Dockerfile.drupal.ubi
    3) drupal.zip
    4) drupal_schema.sql

Rename 2 below files:-

    #cp Dockerfile.drupal.ubi Dockerfile
    #cp automate_drupal.sh.txt automate_drupal.sh
    #chmod +x automate_drupal.sh


Now we can create an image  using below command from dockerfile (Dockerfile.drupal.ubi i.e Dockerfile)

     #docker build -t drupal_image .
     #docker images


Then run a container using that image.

    #docker run -it -d drupal_image /bin/bash
    #docker ps
    #docker exec -it a025d0a771a8 /bin/bash

Live drupal webserver will be available at http://<ip>:8081

After that we need to run below command inside container.Run it in single command . 
Step 1 will take sometime to exec. (configuration)

Step 1:- 

    su - postgres -c 'if [[ $(psql -l | grep dru2_pg) ]]; then     echo "Database already configured..."; else    /usr/pgsql-13/bin/pg_ctl -D /var/lib/pgsql/13/data/ start;     /usr/pgsql-13/bin/createdb -T template0 dru2_pg;     psql dru2_pg < /opt/app-root/src/drupal_schema.sql; fi'

Step 2:-

    /usr/sbin/httpd -k start;


step 3 Go to :-

    #cd /opt/app-root/src/drupal/modules

Clone the module which you wanted to test :-

/usr/sbin/httpd -k start

export PACKAGE_NAME=externalauth

cd /opt/app-root/src/drupal/modules
git clone  https://git.drupalcode.org/project/$PACKAGE_NAME
cd $PACKAGE_NAME
git checkout 8.x-1.3

cd /opt/app-root/src/drupal/core/
../vendor/bin/drush pm:enable $PACKAGE_NAME

RUN TEST:---

bash-4.4# ../vendor/phpunit/phpunit/phpunit ../modules/externalauth/tests
PHPUnit 7.5.20 by Sebastian Bergmann and contributors.

Testing ../modules/externalauth/tests
................                                                  16 / 16 (100%)

Time: 4.21 seconds, Memory: 4.00 MB

OK (16 tests, 41 assertions)

bash-4.4# ../vendor/phpunit/phpunit/phpunit ../modules/externalauth/tests/src/Unit
PHPUnit 7.5.20 by Sebastian Bergmann and contributors.

Testing ../modules/externalauth/tests/src/Unit
...............                                                   15 / 15 (100%)

Time: 992 ms, Memory: 4.00 MB

OK (15 tests, 34 assertions)

bash-4.4# ../vendor/phpunit/phpunit/phpunit ../modules/externalauth/tests/src/Kernel
PHPUnit 7.5.20 by Sebastian Bergmann and contributors.

Testing ../modules/externalauth/tests/src/Kernel
.                                                                   1 / 1 (100%)

Time: 3.29 seconds, Memory: 4.00 MB

OK (1 test, 7 assertions)


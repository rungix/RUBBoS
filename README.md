RUBBoS
------

## Overview

RUBBoS is a bulletin board benchmark modeled after an online news forum like [Slashdot](http://slashdot.org).

RUBBoS implements the essential bulletin board features of the Slashdot site.
In particular, as in Slashdot, it supports discussion threads.
A discussion thread is a logical tree, containing a story at its root and a number of comments for that story, which may be nested.
Users have two different levels of authorized access: regular user and moderator.
Regular users browse and submit stories and comments.
Moderators in addition review stories and rate comments.

RUBBoS is a free, open source initiative available at the [OW2 Consortium](http://jmob.ow2.org/rubbos.html) web site.

## Incarnations

The [OW2 RUBBoS](http://rubbos.ow2.org) web site offers several implementations of the RUBBoS benchmark, that use three different technologies: *PHP*, *Java servlets* and *Enterprise Java Bean* (EJB).

In *PHP* and *Java servlets*, the application programmer is responsible for writing the SQL queries.
An *EJB* server provides a number of services such as database access (JDBC), transactions (JTA), messaging (JMS), naming (JNDI) and management support (JMX).
The EJB server manages one or more EJB containers.
The container is responsible for providing component pooling and lifecycle management, client session management, database connection pooling, persistence, transaction management, authentication and access control.
EJB containers automatically manage bean persistence, relieving the programmer of writing SQL code.

Currently, we focus only the *PHP* and a *Java Servlets* implementation of RUBBoS.


## Installation

### Frontend

#### PHP Incarnation

1. Login to your Web server machine:
2. Get the RUBBoS project:
    ```shell
    $ git clone https://github.com/sguazt/RUBBoS.git
    ```
3. Copy the PHP sources to the *server root* directory `$SERVER_ROOT` of your Web server (e.g., `$SERVER_ROOT` may be `/var/www`):
    ```shell
    $ mkdir -p $SERVER_ROOT/rubbos
    $ cp -r RUBBoS/php $SERVER_ROOT/rubbos/PHP
    ```
4. Edit the `$SERVER_ROOT/rubbos/PHP/PHPprinter.php` file to set the host name or IP `$BACKEND_HOST` of the machine where your DBMS is running, and the user name `$DB_USER` (e.g., `rubbos`) and password `$DB_PASSWORD` (e.g., `rubbos`) to access to the RUBBoS database, that is:
    ```php
    $link = mysql_pconnect("$BACKEND_IP", "$DB_USER", "$DB_PASSWORD") or die ("ERROR: Could not connect to database");
    ```
5. Update the configuration of your Web server to set `$SERVER_ROOT/rubbos/PHP` as the Web server *document root* (e.g., for the Apache Web Server, this is the `DocumentRoot` property in the `httpd.conf` file).
6. Update your PHP property file `$PHP_INI` (e.g., `$PHP_INI` may be `/etc/php.ini`). Typical settings include:
    ```
    ;upload_max_filesize = 2M
    short_open_tag = On
    date.timezone = UTC
    ; The following changes are useful for debugging purpose
    track_errors = On
    display_errors = On
    session.bug_compat_42 = On
    session.bug_compat_warn = On
    ```
7. Reboot

### Java Servlets Incarnation

1. Login to your Web server machine:
2. Get the RUBBoS project and move to the *servlets* incarnation directory:
    ```shell
    $ git clone https://github.com/sguazt/RUBBoS.git
    $ cd RUBBoS/servlets
    ```
3. Edit configuration files:
    - Edit the `../user.properties` file to set the property `j2ee.home` to the path where there are the J2EE jars (e.g., for Apache Tomcat this is the path to the `tomcat-servlet-api.jar` file).
    - Edit the `./src/conf/mysql.properties` file to set the host name or IP `$BACKEND_HOST` of the machine where your DBMS is running, and the user name `$DB_USER` (e.g., `rubbos`) and password `$DB_PASSWORD` (e.g., `rubbos`) to access to the RUBBoS database, that is:
        ```
        datasource.url          jdbc:mysql://$BACKEND_HOST/rubbos
        datasource.username     $DB_USER
        datasource.password     $DB_PASSWORD
        ```
    - Edit the `./src/java/edu/rice/rubbos/servlets/Config.java` to set the path to HTML files `$HTML_PATH` (e.g., `/usr/share/tomcat/webapps/rubbos`) and the path to the database property file `$DB_PROPERTY_FILE` (e.g., `/usr/share/tomcat/webapps/rubbos`), that is:
    ```java
    public static final String HTMLFilesPath                 = "/home/margueri/RUBBoS/Servlet_HTML";
    public static final String DatabaseProperties            = "/home/margueri/RUBBoS/Servlets/mysql.properties";
    ```
4. Build the war file:
    ```shell
    $ ant
    ```
5. Reboot


# Credits and Disclaimer

The RUBBoS bulletin board benchmark was originally developed by *Emmanuel Cecchet* and *Julie Marguerite* at *Rice University* under the *DynaServer* project.
Then, it became part of the [OW2 JMOB](http://jmob.ow2.org) where it is currently available at [this page](http://jmob.ow2.org/rubbos.html).
The last release of the OW2 RUBBoS is version 1.2.2, which dates back to October 2004 (see [here](http://forge.ow2.org/projects/rubbos/rubbos/)).

While the benchmark is well-implemented, no updates have been made since 2005.
This has caused some incompatibilities with newer versions of the software which RUBBoS depends on.

Recently, to cope with these issues, *Michael Mior* created a [Github project](https://github.com/michaelmior/RUBBoS) for the OW2 RUBBoS 1.2.2.
The Michael's repository provides some updates to resolve these incompatibilities.

I begun contributing to RUBBoS by forking the Micheal's repository and issuing some pull requests for patches and improvements.
As of December 2014, Michael seems out of time for maintaining its RUBBoS repository, as [he announces that](https://github.com/michaelmior/RUBBoS/blob/master/CONTRIBUTING.md):

> it is unlikely that I will have the time to test and accept any major changes

So, I decided to stop working to the forked project (still available [here](https://github.com/sguazt/RUBBoS_fork_of_michaelmior) for logging purpose) and I started this new Github project which includes all the work done in the Michael's repository until December 26th, 2014.

Please, note that I'm going to use RUBBoS for research purpose and for a limited time only.
So, don't expect frequent updates and quick feedback.

Anyway, if you like it you are invited to contribute.

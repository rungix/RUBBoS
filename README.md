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

### Frontend Installation

By *frontend*, we mean the machine where the Web server is running.
In the following, we don't provide instructions to install a Web server.
To do so, please refer to the user documentation of your Web server.

#### PHP Incarnation

In the following, we assume that you use the *Apache Web server* and that a working installation is already installed on your system.

1. Login to your Web server machine:

2. Get the RUBBoS project:

    ```shell
    $ git clone https://github.com/sguazt/RUBBoS.git
    $ export RUBBOS_HOME=$PWD/RUBBoS
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

5. Edit the Apache Web server `httpd.conf` file (typically located under `/etc/httpd/conf` directory) to set `$SERVER_ROOT/rubbos/PHP` as its `DocumentRoot`.

    ```
    DocumentRoot $SERVER_ROOT/rubbos/PHP
    ```

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

#### Java Servlets Incarnation

In the following, we assume that you use the *Apache Tomcat* J2EE container and that a working installation is already installed on your system.

1. Login to your Web server machine:

2. Get the RUBBoS project and move to the *servlets* incarnation directory:

    ```shell
    $ git clone https://github.com/sguazt/RUBBoS.git
    $ export RUBBOS_HOME=$PWD/RUBBoS
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

    - Edit the `./src/java/edu/rice/rubbos/servlets/Config.java` to edit the path to HTML files and the path to the database property file. Specifically, in the following, you need to replace  `/usr/share/tomcat/` prefix with the server root of your Apache Tomcat installation:

        ```java
        public static final String HTMLFilesPath      = "/usr/share/tomcat/webapps/rubbos";
        public static final String DatabaseProperties = "/usr/share/tomcat/webapps/rubbos/WEB-INF/classes/META-INF/mysql.properties";
        ```

4. Build the `rubbos.war` war file:

    ```shell
    $ ant clean build
    ```

   In case of no error, you can find the war file in `./dist/rubbos.war`.

5. Deploy the `rubbos.war` war into your J2EE container (e.g., in case of Apache Tomcat, copy `dist/rubbos.war` into the `webapps` sub-directory located under the Apache Tomcat root directory, like `/usr/share/tomcat/webapps`).

6. Restart your J2EE container.

### Backend Installation

By *backend*, we mean the machine where the DBMS server is running.
In the following guide, we asssume you use the MySQL server as the DBMS server.

1. Login to your DBMS server machine:

2. Get the RUBBoS project:

    ```shell
    $ git clone https://github.com/sguazt/RUBBoS.git
    $ export RUBBOS_HOME=$PWD/RUBBoS
    ```

3. Create the `rubbos` database schema and populate with minimal data:

    ```shell
    $ cd $RUBBOS_HOME
    $ mysql -uroot rubbos < database/rubbos.sql
    $ mysql -uroot rubbos < database/test.sql
    ```

### Client Installation

By *client*, we mean the component that submits the workload to the RUBBoS application.
You need to repeat this installation step for each remote client you want to run the workload against the RUBBoS application.
One of this client machine will be the *master client*, that is the client that will coordinate the other ones.
Each remote client will require installations of *Java JRE* and of the *sysstat* application.
Also, each remote client must be accessible from the master client by means of either RSH or SSH.
In the last case, you need to copy the SSH public key of the master client to each of the other remote client to avoid to be asked for a password on connection.

1. Login to your client machine:

2. Get the RUBBoS project:

    ```shell
    $ git clone https://github.com/sguazt/RUBBoS.git
    $ export RUBBOS_HOME=$PWD/RUBBoS
    ```

3. Compile the client code to get the jar file:

    ```shell
    $ cd $RUBBOS_HOME/client
    $ ant clean dist
    ```

   In case of no error, you can find the jar file in `./dist/rubbos_client.jar`.

4. Compile the utility to flush the cache:

    ```shell
    $ cd $RUBBOS_HOME/client
    $ gcc bench/flush_cache.c -o bench/flush_cache 
    ```

## Database Initialization

There are two possible ways to populate the RUBBoS database.
In the following guide, we asssume you use the MySQL server as the DBMS server.

1. Login to your DBMS machine.

2. Download one of the following database dump files from the [OW2 Consortium](http://www.ow2.org) Web site:
  - *Small DB*

        ```shell
        $ cd $RUBBOS_HOME/database
        $ wget http://download.forge.ow2.org/rubbos/smallDB-rubbos.tgz
        ```

  - *Expanded data set*

        ```shell
        $ cd $RUBBOS_HOME/database
        $ wget http://download.forge.ow2.org/rubbos/rubbos-expanded-dataset.tar.bz2
        ```

3. Once you have uncompressed the downloaded file, populate the database with one of the following command:
  1. For older versions of MySQL, use the `load.sql` file (before of using it you need to update the path to data files inside it):

        ```shell
        $ mysql -uroot rubbos < load.sql
        ```

  2. For recent versions of MySQL, use the `mysqlimport` command (suggested method):

      ```shell
      $ mysqlimport -uroot \
                    --local \
                    --verbose \
                    --delete \
                    --fields-terminated-by="\\t" \
                    rubbos \
                    ./users.data \
                    ./stories.data \
                    ./comments.data \
                    ./old_stories.data \
                    ./old_comments.data \
                    ./submissions.data \
                    ./moderator_log.data
      ```

## Client Execution

1. Login to your master client machine:

2. Setup the workload files by editing each of `$RUBBOS_HOME/bench/rubbos_properties_*` file for your configuration.
   The simplest way to change the amount of load generated is to change the number of remote clients as well as `workload_number_of_clients_per_node`.
   You can also setup the type of connection (e.g., SSH or RSH).
   Finally, `workload_user_transition_table` and `workload_author_transition_table` can be set to the defaults found in the `workload` subdirectory.

3. Run the client emulator:

    ```shell
    $ cd $RUBBOS_HOME
    $ java -Xmx256m -Xms128m -server -classpath .:client/dist/rubbos_client.jar edu.rice.rubbos.client.ClientEmulator 
    ```

4. After the execution, you can plot some graphs of the collected data.
   To produce these graphs of the data, the master client will require the *gnuplot* program.


## Credits and Disclaimer

The RUBBoS bulletin board benchmark was originally developed by *Emmanuel Cecchet* and *Julie Marguerite* at *Rice University*/*INRIA* under the *DynaServer* project.
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

# PostgreSQL 11 - Administration, Scalability, High-Availability ..

    + What makes PostgreSQL different?
    The PostgreSQL project focuses on the following objectives:
    Robust, high-quality software with maintainable, well-commented code
    Low-maintenance administration for both embedded and enterprise use Standards-compliant SQL, interoperability, and compatibility Performance, security, and high availability
    What surprises many people is that PostgreSQL's feature set is more similar to Oracle or SQL Server than it is to MySQL. The only connection between MySQL and PostgreSQL is that these
    two projects are open source; apart from that, the features and philosophies are almost totally different.
    One of the key features of Oracle, since Oracle 7, has been snapshot isolation, where readers don't block writers and writers don't block readers. You may be surprised to learn that PostgreSQL
    was the first database to be designed with this feature, and it offers a complete implementation. In PostgreSQL, this feature is called Multiversion Concurrency Control (MVCC).

    + Performance and concurrency
    PostgreSQL 11 can achieve significantly more than 1,000,000 reads per second on a 4-socket server, and it benchmarks at more than 30,000 write transactions per second with full durability, depending upon your hardware.
    With advanced hardware, even higher levels of performance are possible.
    PostgreSQL has an advanced optimizer that considers a variety of join types, utilizing user data statistics to guide its choices. PostgreSQL provides the widest range of index types of any commonly available database server,
    fully supporting all data types.
    PostgreSQL provides MVCC, which enables readers and writers to avoid blocking each other.
    Taken together, the performance features of PostgreSQL allow a mixed workload of transactional systems and complex search and analytical tasks. This is important because it means we don't always need to unload our data from
    production systems and reload it into analytical data stores just to execute a few ad hoc queries. PostgreSQL's capabilities make it the database of choice for new systems, as well as the correct long-term choice in almost every case.

    + Scalability
    PostgreSQL 11 scales well on a single node up to four CPU sockets. PostgreSQL efficiently runs up to hundreds of active sessions, and up to thousands of connected sessions when using a session pool. Further scalability is
    achieved in each annual release.
    PostgreSQL provides multi-node read scalability using the Hot Standby feature. Multi-node write scalability is under active development. The starting point for this is Bi-Directional Replication

    + Connect to postgreSQL :
    $ psql postgres://mdrahali:mohamed-2@localhost:5432/blog

    $ SELECT current_database() ;
    $ SELECT current_user;

    - The next command shows the IP address and port of the current connection, unless you are using Unix sockets, in which case both values are NULL:
    $ SELECT inet_server_addr(), inet_server_port();

    $ SELECT version();

    - Check some meta infos :
    $ \conninfo

    Enabling access for network/remote users
    PostgreSQL comes in a variety of distributions. In many of these, you will note that remote access is initially disabled as a security measure.

    - Change listening address :
    $ cd /usr/local/Cellar/postgresql/12.3_4/share/postgresql/
    $ code postgresql.conf.sample

    - Edit this line :
    # - Connection Settings -
    #listen_addresses = 'localhost'		# what IP address(es) to listen on;

    to :

    # - Connection Settings -
    listen_addresses = '*'		# what IP address(es) to listen on;

    - Add the following line as the first line of pg_hba.conf to allow access to all databases for all users with an encrypted password:

    $ cd /usr/local/Cellar/postgresql/12.3_4/share/postgresql
    $ code pg_hba.conf.sample

    # TYPE   DATABASE   USER        CIDR-ADDRESS   METHOD
    host      all        all          0.0.0.0/0       md5

    - Restart PostgreSQL server :

    $ pg_ctl -D /usr/local/var/postgres stop
    $ pg_ctl -D /usr/local/var/postgres start

    - How it works :

    The listen_addresses parameter specifies which IP addresses to listen to. This allows you to flexibly enable and disable listening on interfaces of multiple network cards (NICs) or virtual networks on the same system. In most cases, we want to accept connections on all NICs, so we use *, meaning all IP addresses.
    The pg_hba.conf file contains a set of host-based authentication rules. Each rule is considered in sequence, until one rule fires or the attempt is specifically rejected with a reject method.
    The preceding rule means that a remote connection that specifies any user or database on any IP address will be asked to authenticate using an MD5- encrypted password. The following are the parameters required for MD5- encrypted passwords:

    Type: For this, host means a remote connection.
    Database: For this, all means for all databases. Other names match exactly, except when prefixed with a plus (+) symbol, in which case we mean a group role rather than a single user. You can also specify a comma-separated list of users, or use the @ symbol to include a file with a list of users.
              You can even specify sameuser, so that the rule matches when you specify the same name for the user and database. User: For this, all means for all users. Other names match exactly, except when prefixed with a plus (+) symbol, in which case we mean a group role rather than a single user.
              You can also specify a comma- separated list of users, or use the @ symbol to include a file with a list of users.
    CIDR-ADDRESS: This consists of two parts: an IP address and a subnet mask. The subnet mask is specified as the number of leading bits of the IP address that make up the mask. Thus, /0 means 0 bits of the IP address, so that all IP addresses will be matched. For example, 192.168.0.0/24 would mean
                  matching of the first 24 bits, so any IPs address of the form 192.168.0.x would match. You can also use samenet or samehost.
    Method: For this, md5 means that PostgreSQL will ask the client to provide a password encrypted with MD5. Another common setting is trust, which effectively means no authentication. Other authentication methods include GSSAPI, SSPI, LDAP, RADIUS, and PAM. PostgreSQL connections can also be made using SSL,
            in which case client SSL certificates provide authentication.


    + Using graphical administration tools :

    - Launch PGADMIN -> click on connect to a server -> add credentials :

    Name : localserver
    Host : localhost
    user : mdrahali
    password : m****

    - click on connect.

    - Two ways to connect to a PostgreSQL :

    $ psql postgres://mdrahali:mohamed-2@localhost:5432/blog
    $ psql -h localhost -p 5432 -d blog -U mdrahali
    command prompt will give you space to enter your password in a secret way.

    - execute sql command from psql scripting tool :
    $ psql -d blog -c "SELECT current_time" ;

    - The -c command is non-interactive. If we want to execute multiple commands, we can write those commands in a text file and then execute them using the -f option. This command loads a very small and simple set of examples:
    $ psql -f examples.sql

    - The psql tool can also be used with both the -c and -f modes together; each one can be used multiple times. In this case, it will execute all the commands consecutively:
    $ psql -c "SELECT current_time" –f examples.sql -c "SELECT current_time"

    - connect to the database :
    $ psql -d blog

    - A meta-command is a command for the psql client, whereas SQL is sent to the database server. An example of a meta-command is \q, which tells the client to disconnect. All lines that begin with \ (backslash) as the
    first nonblank character are presumed to be meta-commands of some kind.

    + get help on Meta-commands :
    $ \?

    + get help on SQL commands :
    $ \h

    += Consider the following snippet as an example:
       postgres=# \h DELETE
       Command: DELETE
       Description: delete rows of a table
       Syntax:
       [ WITH [ RECURSIVE ] with_query [, ...] ]
       DELETE FROM [ ONLY ] table [ [ AS ] alias ]
           [ USING usinglist ]
           [ WHERE condition | WHERE CURRENT OF cursor_name ]
           [ RETURNING * | output_expression [ AS output_name ] [,]]

    -> Autocompletion Feature :
    ++ You'll get a lot of benefits from tab completion, which will fill in the next part of the syntax when you press the Tab key. This also works for object names, so you can type in just the first few letters and then
       press Tab; all the options will be displayed. Thus, you can type in just enough letters to make the object name unique, and then hit Tab to get the rest of the name.

    Changing your password securely
    If you are using password authentication, then you may wish to change your password from time to time.

    $ psql -d blog
    $ SET password_encryption = 'scram-sha-256';
    $ \password

    - Make sure you use SCRAM-SHA-256 encryption, not the older and easily compromised md5 encryption. Whatever you do, don't use postgres as your password. This will make you vulnerable to idle hackers, so make it a little more difficult than that!s

    + You may wonder, how long has it been since the server started?

    $ SELECT date_trunc('second', current_timestamp - pg_postmaster_start_time()) as uptime;

    Postgres stores the server start time, so we can access it directly, as follows:
    $ SELECT pg_postmaster_start_time();

    - Locating the database server files
    Database server files are initially stored in a location referred to as the data directory. Additional data files may also be stored in tablespaces, if any exist.

    The following are the system default data directory locations:

    Debian or Ubuntu systems: /var/lib/postgresql/MAJOR_RELEASE/main
    Red Hat RHEL, CentOS, and Fedora: /var/lib/pgsql/data/
    Windows: C:\Program Files\PostgreSQL\MAJOR_RELEASE\data

    initdb -D /usr/local/pgsql/data

    # service postgresql initdb
    # systemctl enable postgresql
    # systemctl start postgresql

    - locating logs :

    The following are the default server log locations:
    Debian or Ubuntu systems: /var/log/postgresql
    Red Hat, RHEL, CentOS, and Fedora: /var/lib/pgsql/data/pg_log


    - Locating the database's system identifier
    Each database server has a system identifier assigned when the database is initialized (created). The server identifier remains the same if the server is backed up, cloned, and so on.
    Many actions on the server are keyed to the system identifier, and you may be asked to provide this information when you report a fault.

    In order to display the system identifier, we just need to launch the following command:
    $ pg_controldata <data-directory> | grep "system identifier"

    Note that the preceding syntax will not work on Debian or Ubuntu systems, for the same reasons explained for initdb in the Locating the database server files recipe. However, in this case, there is no postgresql-common alternative, so if you must run pg_controldata, you need to specify the full path to the executable, as in this example:
    /usr/lib/postgresql/11/bin/pg_controldata $PGDATA

    - Listing all databases on PostgreSQL server :
    $ psql -l

    $ select datname from pg_database;

    + First of all, look at the use of the \x command. It makes the output in psql appear as one column per line, rather than one row per line.

    $ \x
    $ select * from pg_database;

    - List database Tables :

    $ psql -d blog -c "\d"

    + How much disk space does a database use?
      It is very important to allocate sufficient disk space for your database. If the disk gets full, it will not corrupt the data, but it might lead to database server panic and then consequent shutdown.

    $ psql -d blog

    - Size of the current database :
    $ SELECT pg_database_size(current_database());


    - However, this is limited to only the current database. If you want to know the size of all the databases together, then you'll need a query such as the following:
    $ SELECT sum(pg_database_size(datname)) from pg_database;

    - How much disk space does a table use?
    The maximum supported table size is 32 TB and it does not require large file support from the operating system. The file system size limits do not impact the large tables, as they are stored in multiple 1 GB files.

    $ select pg_relation_size('taggit_tag');

    We can also see the total size of a table, including indexes and other related spaces, as follows:
    $ postgres=# select pg_total_relation_size('taggit_tag');

    $ SELECT pg_size_pretty(pg_relation_size('taggit_tag'));

    - The following basic query will tell us the 10 biggest tables:

    $ psql -d postgres

    $  SELECT table_name,pg_relation_size(table_schema || '.' || table_name) as size
       FROM information_schema.tables
       WHERE table_schema NOT IN ('information_schema', 'pg_catalog')
       ORDER BY size DESC
       LIMIT 2;

    - How many rows are there in a table?
    There is no limit on the number of rows in a table but it is limited to available disk space and memory/swap space. If you are storing rows that exceed 2 KB aggregated data size, then the maximum number of rows may be limited to 4 billion or less.

    $ SELECT count(*) FROM topology;

    - List extensions :
    $ SELECT * FROM pg_extension;

    + Object dependencies :

    1- We'll use the following simple database to understand and investigate them: 1. Create two tables as follows:
           CREATE TABLE orders (
           orderid integer PRIMARY KEY
           );
           CREATE TABLE orderlines (
           orderid integer
           ,lineid smallint
           ,PRIMARY KEY (orderid, lineid)
           );

    2. Now, we add a link between them to enforce what is known as referential integrity, as follows:
               ALTER TABLE orderlines ADD FOREIGN KEY (orderid)
               REFERENCES orders (orderid);
    3. If we try to drop the referenced table, we get the following message:
               DROP TABLE orders;
               ERROR: cannot drop table orders because other objects depend on it
               DETAIL: constraint orderlines_orderid_fkey on table orderlines depends on table orders
               HINT: Use DROP ... CASCADE to drop the dependent objects too.

    Be very careful! If you follow the hint, you may accidentally remove all the objects that have any dependency on the orders table.
    You might think that this would be a great idea, but it is not the right thing to do. It might work, but we need to ensure that it will work.

    - solution :

    + get infos about a table before deleting it :
    $ \d+ orders

    SELECT * FROM pg_constraint
    WHERE confrelid = 'orders'::regclass;

    Changing parameters in your programs
    PostgreSQL allows you to set some parameter settings for each session or transaction.

    Execute the following steps to set custom parameters settings:
    1. You can change the value of a setting during your session, like this:
               SET work_mem = '16MB';
    2. This value will then be used for every future transaction. You can also change it only for the duration of the current transaction:
               SET LOCAL work_mem = '16MB';
    3. The setting will last until you issue this command:
               RESET work_mem;
    4. Alternatively, you can issue the following command:
               RESET ALL;

    - Finding the current configuration settings :
    + Display memory allocated :
    $ SHOW work_mem;

    + Display location of config file of postgres :
    $ SHOW config_file;
    $ code /usr/local/var/postgres/postgresql.conf

    - change  work_mem from 4MB to 5MB.

    + Restart Postgres :
    $ brew services restart postgresql

    + Recheck work_mem :
    $ SHOW work_mem;

    + Which parameters are at non- default settings?
    - Often, we need to check which parameters have been changed, or whether our changes have taken effect correctly.

    + In this recipe, we will show you how to use SQL capabilities to list only those parameters whose value in the current session differs from the system-wide default value.

    - We write an SQL query that lists all parameter values, excluding those whose current value is either the default or set from a configuration file:
    postgres=# SELECT name, source, setting
                           FROM pg_settings
                           WHERE source != 'default'
                           AND source != 'override'
                           ORDER by 2, 1;

    The setting column of pg_settings shows the current value, but you can also look at the boot_val and reset_val parameters. The boot_val parameter shows the value that was assigned
    when the PostgreSQL database cluster was initialized (initdb), while reset_val shows the value that the parameter will return to if you issue the RESET command.

    + Query to see the default value and the new value :

    postgres=# SELECT name, source, setting, boot_val, reset_val
                           FROM pg_settings
                           WHERE source != 'default'
                           AND source != 'override'
                           ORDER by 2, 1;


    - After changing the required parameters, we issue a reload command to the server, forcing PostgreSQL to re-read the postgresql.conf file (and all other configuration files).

    + check data directory :
    $ show data_directory;

    + Reload Changed parameters

    $ pg_ctl -D /usr/local/var/postgres reload
    $ pg_ctl -D /usr/local/var/postgres restart

    - For Debian, Ubuntu the command is :
    $ pg_ctlcluster 11 main reload
    $ pg_ctlcluster 11 main restart

    - List all extensions installed on Postgres Server:
    $ psql -d postgres
    $ SELECT * FROM pg_available_extensions ORDER BY name;

    - Now, we can list all the objects in the postgis extension, as follows:
    $ \dx+ postgis

    - Drop a function :
    $ DROP FUNCTION dblink_close(text);

    - Extensions might have dependencies, too. The cube and earthdistance contrib extensions are a good example, since the latter depends on the former:
    postgres=# CREATE EXTENSION earthdistance;

    $ DROP EXTENSION cube;

    - drop extension and it all objects that depend on extension:
    $ DROP EXTENSION cube CASCADE;

    - Update Version of an Extension :
    $ ALTER EXTENSION mytext UPDATE TO '1.1';

    - Start Postgres on Linux System :
    $ sudo systemctl start postgresql

    - Stop server Fastly :
    $ pg_ctlcluster 11 main stop -m fast

    + When you do a fast stop, all users have their transactions aborted and all connections are disconnected. This is not very polite to users, but it still treats the server and its data with care, which is good.

    - Stop server Immediately :
    $ pg_ctlcluster 11 main stop -m immediate

    + Preventing new connections :
    In certain emergencies, you may need to lock down the server completely, or just prevent specific users from accessing the database. It's hard to foresee all the situations in which you might need to do this,
    so we will present a range of options.

    Restrict the connections for a specific database to zero, by setting the connection limit to zero:
           $ ALTER DATABASE foo_db CONNECTION LIMIT 0;

    This will limit normal users from connecting to that database, though it will still allow superuser connections.
    Restrict the connections for a specific user to zero by setting the connection limit to zero (see the Restricting users to only one session each recipe):
           $ ALTER USER foo CONNECTION LIMIT 0;


    We can restrict users to only one connection using the following command:
       postgres=# ALTER ROLE fred CONNECTION LIMIT 1;

    + Create Multiple Database Servers on one system :
    $ sudo -u postgres pg_createcluster 11 main2
    $ sudo -u postgres pg_ctlcluster 11 main2 start

    The data and configuration files are stored inside the /var/lib/postgresql/11/main2/ and /etc/postgresql/11/main2/ directories, respectively, giving the new database the next unused port number, for example, 5433 if this
    is the second PostgreSQL server on that machine.

    $ psql --cluster 11/main2

    With Red Hat systems, you will need to run initdb directly, selecting your directories carefully:
    1. First, initialize your data directory with something such as the following:
               sudo -u postgres initdb -D /var/lib/pgsql/datadir2

    + Removing duplicate :

    $ CREATE UNLOGGED TABLE dup_cust AS
       SELECT *
       FROM cust
       WHERE customerid IN
        (SELECT customerid
         FROM cust
         GROUP BY customerid
         HAVING count(*) > 1);


    $ DELETE FROM new_cust
           USING dups_cust
           WHERE new_cust.customerid = dups_cust.customerid
           AND new_cust.ctid != dups_cust.min_ctid;

    $ COMMIT;
    $ VACUUM new_cust;

    + Genrating a Test Sample :
    $ SELECT date(t)
           FROM generate_series(now(),
             now() + '1 week', '1 day') AS f(t);

    + Dump data from a database to a flat file :

    $ touch csv.load
    LOAD CSV
        FROM '/tmp/file.csv' (x, y, a, b, c, d)
        INTO postgresql://postgres@localhost:5432/postgres?csv (a, b, d, c)
        WITH truncate,
             skip header = 1,
             fields optionally enclosed by '"',
             fields escaped by double-quote,
             fields terminated by ','
         SET client_encoding to 'latin1',
             work_mem to '12MB',
             standard_conforming_strings to 'on'
      BEFORE LOAD DO
       $$ drop table if exists csv; $$,
       $$ create table csv (
           a bigint,
           b bigint,
           c char(2),
           d text
    ); $$;

    $ brew install pgloader
    $ cd /private/tmp/
    $ touch file.csv
    $ pgloader csv.load

    + Create a procedure to execute a transaction that update multiple employees :
    As an example, let's consider a case where we need to update all employees with the A2 job grade, giving each person a 2% pay rise:

       CREATE PROCEDURE annual_pay_rise ()
       LANGUAGE plpgsql AS $$
       DECLARE
       c CURSOR FOR
       SELECT * FROM employee
           WHERE job_code = 'A2';
       BEGIN
       FOR r IN c LOOP
       UPDATE employee
       SET salary = salary * 1.02
       WHERE empid = r.empid;
        IF mod (r.empid, 100) = 0 THEN
       COMMIT;
       END IF;
       END LOOP;
       END; $$;

    Execute the preceding procedure like this:
       CALL annual_pay_rise();

    Typical user role
    The minimal production database setup contains at least two types of users, namely administrators and end users, where administrators can do everything (they are superusers), and end users can only do very little, usually just modifying the data in only a few tables and reading from a few more.
    It is not a good idea to let ordinary users create or change database object definitions, meaning that they should not have the CREATE privilege on any schema, including PUBLIC.

    + Create superuser :
    $ CREATE USER username SUPERUSER;

    + Update user from superuser to a normal user :
    $ ALTER USER username NOSUPERUSER;

    $ ALTER USER username SUPERUSER;

    ++ The PostgreSQL system comes set up with at least one superuser. Most commonly, this superuser is named postgres, but occasionally it adopts the same name as the system user who owns the database directory and with whose rights the PostgreSQL server runs.

    - Revoking user access to a table :
    This recipe answers the question: how do I make sure that user X cannot access table Y?

    To revoke all rights on the table1 table from the user2 user, you must run the following SQL command:
    $ REVOKE ALL ON table1 FROM user2;
    $ REVOKE role3 FROM user2; # because if we let user2 make apart from role3 all users that are role3 will not have access to table1.


    + Display Access priveliged :
    $ \dp postgres

    - Grant access certains operatons to some users :
    The following is a sample extract from the database creation script:
       CREATE TABLE table1(
       ...
       );
       GRANT SELECT ON table1 TO webreaders;
       GRANT SELECT, INSERT, UPDATE, DELETE ON table1 TO editors;
       GRANT ALL ON table1 TO admins;

    + Create a User :
    $ createuser bob

    - Temporarily preventing a user from connecting
    $ alter user bob nologin;
    $ alter user bob login;

    NB: telling PostgreSQL not to let the user log in. It does not kick out already connected users.

    Limiting the number of concurrent connections by a user
    The same result can be achieved by setting the connection limit for that user to 0:
    $ pguser=# alter user bob connection limit 0;

    To allow 10 concurrent connections for the bob user, run this command:
    $ pguser=# alter user bob connection limit 10;

    To allow an unlimited number of connections for this user, run the following:
    $ pguser=# alter user bob connection limit -1;

    - Forcing NOLOGIN users to disconnect :
    In order to make sure that all users whose login privileges have been revoked are disconnected right away, run the following SQL statement as a superuser:
       SELECT pg_terminate_backend(pid)
         FROM pg_stat_activity a
          JOIN pg_roles r ON a.usename = r.rolname AND NOT rolcanlogin;

    This disconnects all users who no longer are allowed to connect by terminating the backends opened by these users.

    

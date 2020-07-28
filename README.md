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

    + check data directory - PGDATA :
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

    To give the bob role the ability to create new databases, run this:
    $ ALTER ROLE BOB WITH CREATEDB;

    To give the bob role the ability to create new users, run the following:
    $ ALTER ROLE BOB WITH CREATEROLE;

    + Connecting using SSL
    $ Set ssl = on in postgresql.conf and restart the database.

    + Writing a script that either succeeds entirely or fails entirely :

    The basic way to ensure that we get all commands successful or none at all is to literally wrap our script into a transaction, as follows:
       BEGIN;
       command 1;
       command 2;
       command 3;
       COMMIT;

    From psql, you can do this by simply using the -1 or --single-transaction command-line options, as follows:
    bash $ psql -1 -f myscript.sql
    bash $ psql --single-transaction -f myscript.sql
    The -1 option is short, but I recommend using --single-transaction, as it's much clearer which option is being selected.

    + Get More clearer error messages :
    Check the following example to understand the usage of the \errverbose meta-command.
    1. Suppose you hit an error, as in the following query, and verbose reporting was not enabled:
               postgres=# create table wrongname();
               ERROR:  relation "wrongname" already exists
    2. The extra detail that is not displayed is nevertheless remembered by psql, so you can view it as follows:
               postgres=# \errverbose
               ERROR:  42P07: relation "wrongname" already exists
               LOCATION:  heap_create_with_catalog, heap.c:1067

    + Change DateType of column in a table :
     postgres=# ALTER TABLE birthday
                  ALTER COLUMN dob SET DATA TYPE integer
                  USING dob::integer;

     postgres=# ALTER TABLE birthday
       ALTER COLUMN dob SET DATA TYPE date
       USING date(to_date(dob::text, 'YYMMDD') -
             (CASE WHEN dob/10000 BETWEEN 16 AND 69 THEN interval '100
               years'
              ELSE interval '0' END));

     Now, it gives us what we were hoping to see:
       postgres=# select * from birthday;
        name  |    dob
       -------+------------
        simon | 26/09/1969
       (1 row)

    First, we can't move directly from integer to date. We need to convert it into text and then to date. The dob::text statement means cast to text.
    Once we have text, we use the to_date() function to move to a date type.
    This is not enough; our starting data was 690926, which we presume is a date in the YYMMDD format. When PostgreSQL converts this data into a date, it assumes that the two-digit year, 69, is in the current century because it chooses the year nearest to 2020. So, it outputs 2069 rather than 1969. This is why a case statement is added to reduce any year between 16 and 69 to be a date in the previous century by explicitly subtracting an interval of 100 years. We do not need to take away one century for years after 69 because they are already placed in the 20th century.

    + Create a datatype :
     Enumerative data types are defined like this:
       CREATE TYPE satellites_urani AS ENUM ('titania','oberon');
        The other popular case is composite data types, which are created as follows:
           CREATE TYPE node AS
           ( node_name text,
             connstr text,
             standbys text[]);

    - Using materialized views :
    Every time we select rows from a view, we actually select from the result of the underlying query. If that query is slow and we need to use it more than once, then it makes sense to run the query once, save its output as a table, and then select the rows from the latter.
    This procedure has been available for a long time, and there is a dedicated syntax, CREATE MATERIALIZED VIEW, which we will describe in this recipe.

        CREATE TABLE dish
       ( dish_id SERIAL PRIMARY KEY
         , dish_description text
       );


       CREATE TABLE eater
       ( eater_id SERIAL
       , eating_date date
       , dish_id int REFERENCES dish (dish_id)
       );

       INSERT INTO dish (dish_description)
       VALUES ('Lentils'), ('Mango'), ('Plantain'), ('Rice'), ('Tea');

       INSERT INTO eater(eating_date, dish_id)

       SELECT floor(abs(sin(n)) * 365) :: int + date '2014-01-01'
       , ceil(abs(sin(n :: float * n))*5) :: int
       FROM generate_series(1,500000) AS rand(n);

    - Standard View :

    Let's create the following view:
       CREATE VIEW v_dish AS
       SELECT dish_description, count(*)
       FROM dish JOIN eater USING (dish_id)
       GROUP BY dish_description
       ORDER BY 1;
    Then, we'll query it:
       SELECT * FROM v_dish;

    - Metrialized View :

      With a very similar syntax, we create a materialized view with the same underlying query:
       CREATE MATERIALIZED VIEW m_dish AS
       SELECT dish_description, count(*)
       FROM dish JOIN eater USING (dish_id)
       GROUP BY dish_description
       ORDER BY 1;

    The corresponding query yields the same output as before:
       SELECT * FROM m_dish;

    ++ The materialized version is much faster than the non-materialized version. On my laptop, their execution times are 0.2 milliseconds versus 300 milliseconds.

    How it works...
    Creating a non-materialized view is exactly the same as creating an empty table with a SELECT rule, as we discovered from the previous recipe. No data is extracted until the view is actually used.
    When creating a materialized view, the default is to run the query immediately and then store its results, as we do for table content.
    In short, creating a materialized view is slow, but using it is fast. This is the opposite of standard views, which are created instantly and recomputed at every use.

    A materialized view will not automatically change when its constituent tables change. For that to happen, you must issue the following:
    REFRESH MATERIALIZED VIEW m_dish;

    A materialized view cannot be read while it is being refreshed. For that, you need to use the CONCURRENTLY clause at the expense of a somewhat slower refresh.

    - Solution for monitoring resources usage in a database server :

    $ CREATE EXTENSION adminpack;

    + Check weather a user is connected or not :

    $ SELECT datname FROM pg_stat_activity WHERE usename = 'mdrahali';

    + check Computer connected :
    $ SELECT datname, usename, client_addr, client_port,
          application_name FROM pg_stat_activity;


    - The \watch meta-command allows psql users to automatically (and continuously) re-execute a query.

    $ SELECT count(*) FROM pg_stat_activity;
    $ \watch

    + Checking which queries are running currently :

    $ SELECT datname, usename, state, query
              FROM pg_stat_activity;

    $ SELECT datname, usename, state, query
          FROM pg_stat_activity WHERE state = 'active';

    + Watching the longest queries :

        SELECT
        current_timestamp - query_start AS runtime,
        datname, usename, query
        FROM pg_stat_activity
        WHERE state = 'active'
        ORDER BY 1 DESC;

    + On busy systems, you may want to limit the set of queries that are returned to only the first few queries (add LIMIT 10 at the end) or
      only the queries that have been running over a certain period of time. For example, to get a list of queries that have been running for
      more than a minute, use the following query:

       SELECT
           current_timestamp - query_start AS runtime,
           datname, usename, query
       FROM pg_stat_activity
       WHERE state = 'active'
             AND current_timestamp - query_start > '1 min'
       ORDER BY 1 DESC;

    - Watching queries from ps :
    If you want, you can also make queries that are being run show up in process titles, by setting the following configuration in the postgresql.conf file:
       update_process_title = on

    + Finding unused indexes :
    $ SELECT schemaname, relname, indexrelname, idx_scan FROM pg_stat_user_indexes ORDER BY idx_scan;

    - Finding slow SQL statements :

    There are two main kinds of slowness that can manifest themselves in a database.
    The first kind is a single query that can be too slow to be really usable, such as a customer information query in a CRM running for minutes, a password check query running in tens of seconds,
    or a daily data aggregation query running for more than a day. These can be found by logging queries that take over a certain amount of time, either at the client end or in the database.
    The second kind is a query that is run frequently (say a few thousand times a second) and used to run in single-digit milliseconds, but is now running in several tens or even hundreds of milliseconds,
    hence slowing the system down. This kind of slowness is much harder to find.

    $ CREATE EXTENSION pg_stat_statements;
    $ ALTER SYSTEM
             SET shared_preload_libraries = 'pg_stat_statements';

    $ brew services restart postgresql

    $ SELECT calls, total_time, query FROM pg_stat_statements
            ORDER BY total_time DESC LIMIT 10;

    - Another way to find slow queries is to set up PostgreSQL to log them all. So, if you decide to monitor a query that takes over 10 seconds, then set up logging queries over 10 seconds by executing the following command:
    $ postgres=# ALTER SYSTEM
             SET log_min_duration_statement = 10000;


    - Explain and Analyse why a query take too much time :
    $ EXPLAIN (ANALYZE, BUFFERS) .. SQL Query ..
    $ EXPLAIN (ANALYZE, BUFFERS) SELECT calls, total_time, query FROM pg_stat_statements
            ORDER BY total_time DESC LIMIT 10;


    + Enhancing Performancing and reducing execution time using indexes:

       postgres=# CREATE TABLE events(id SERIAL);
       CREATE TABLE
       postgres=# INSERT INTO events SELECT generate_series(1,1000000);
       INSERT 0 1000000
       postgres=# EXPLAIN (ANALYZE)
                    SELECT * FROM events ORDER BY id DESC LIMIT 3;
                               QUERY PLAN
   --------------------------------------------------------------
     Limit  (cost=25500.67..25500.68 rows=3 width=4) \
           (actual time=3143.493..3143.502 rows=3 loops=1)
       ->  Sort  (cost=25500.67..27853.87 rows=941280 width=4)
              (actual time=3143.488..3143.490 rows=3 loops=1)
            Sort Key: id DESC
            Sort Method: top-N heapsort Memory: 25kB
       ->  Seq Scan on events
              (cost=0.00..13334.80 rows=941280 width=4)
              (actual time=0.105..1534.418 rows=1000000 loops=1)
    Planning time: 0.331 ms
     Execution time: 3143.584 ms
       (10 rows)
       postgres=# CREATE INDEX events_id_ndx ON events(id);
       CREATE INDEX
       postgres=# EXPLAIN (ANALYZE)
                    SELECT * FROM events ORDER BY id DESC LIMIT 3;
                               QUERY PLAN
                  Limit  (cost=0.00..0.08 rows=3 width=4) (actual
                  time=0.295..0.311 rows=3 loops=1)
                  ->  Index Scan Backward using events_id_ndx on events
                       (cost=0.00..27717.34 rows=1000000 width=4) (actual
                       time=0.289..0.295 rows=3 loops=1)
                Total runtime: 0.364 ms
                (3 rows)


    - Check for cpu use :

    $ top
    First, watch the percentage of idle CPU from top. If this is in low single digits most of the time, you probably have problems with the CPU's power.


    - Get Query Analysis in JSON format :
    $ EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON) SELECT count(*) FROM topology;

    - Consider the following scenario: a full-text search returns 10,000 documents, but only the first 20 are displayed to users. In this case, order the documents by rank on the server, and return only the top 20 that actually need to be displayed:

       SELECT title, ts_rank_cd(body_tsv, query, 20) AS text_rank
       FROM articles, plainto_tsquery('spicy potatoes') AS query
       WHERE body_tsv @@ query
       ORDER BY rank DESC
       LIMIT 20 ;

    If you need the next 20 documents, don't just query with a limit of 40 and throw away the first 20. Instead, use OFFSET 20 LIMIT 20 to return the next 20 documents.
    To gain some stability so that documents with the same rank still come out in the same order when using OFFSET 20, add a unique field (such as the id column of the articles table) to ORDER BY in both queries:
       SELECT title, ts_rank_cd(body_tsv, query, 20) AS text_rank
       FROM articles, plainto_tsquery('spicy potatoes') AS query
       WHERE body_tsv @@ query
       ORDER BY rank DESC, articles.id
       OFFSET 20 LIMIT 20;

    - Partial Indexing  :

    Another technique for making indexes more usable is partial indexes. Instead of indexing all of the values in a column, you might choose to index only the set of rows that are frequently accessed, for example, by excluding NULL or other unwanted data. By making the index smaller, it will be cheaper to access and fit within the cache better, avoiding pointless work by targeting the index at only the important data. Data statistics are kept for such indexes, so it can also improve the accuracy of query planning. Let's look at an example:
       CREATE INDEX ON customer(id)
        WHERE blocked = false AND subscription_status = 'paid';

    - Parallel Query :

    Take a query that needs to do a big chunk of work, such as the following:
       \timing
       SELECT count(*) FROM accounts;
       count
       ---------
       1000000
       (1 row)
       Time: 261.652 ms
       SET max_parallel_workers_per_gather = 8;
       SELECT count(*) FROM accounts;
       count
       ---------
       1000000
       (1 row)
       Time: 180.513 ms

    By setting the max_parallel_workers_per_gather parameter, we've improved performance using parallel query. Note that we didn't need to change the query at all.

    - Using optimistic locking:

    If you perform work in one long transaction, the database will lock rows for long periods of time. Long lock times often result in application performance issues because of long lock waits:
       BEGIN;
       SELECT * FROM accounts WHERE holder_name ='BOB' FOR UPDATE;
       <do some calculations here>
       UPDATE accounts SET balance = 42.00 WHERE holder_name ='BOB';
       COMMIT;
    If that is happening, then you may gain some performance by moving from explicit locking (SELECT ... FOR UPDATE) to optimistic locking.
    Optimistic locking assumes that others don't update the same record, and checks this at update time, instead of locking the record for the time it takes to process the information on the client side.

    - Crash recovery :
    If PostgreSQL crashes, there will be a message in the server log with the severity level set to PANIC. PostgreSQL will immediately restart and attempt to recover using the transaction log or Write-Ahead Log (WAL).
    Should PostgreSQL crash, the WAL will be replayed, which returns the database to the point of the last committed transaction, and hence ensures the durability of any database changes.

    Two parameters control the occurrence of scheduled checkpoints. The first is checkpoint_timeout, which is the number of seconds until the next checkpoint. While this parameter is time-based, the second parameter, max_wal_size, influences the amount of WAL data that will be written before a checkpoint is triggered; the actual limit is computed from that parameter, taking into account the fact that WAL files can only be deleted after one checkpoint (two in older releases). A checkpoint is called whenever either of these two limits is reached.
    It's tempting to banish checkpoints as much as possible by setting the following parameters:
       max_wal_size = 20GB
       checkpoint_timeout = 3600

    + Hot logical backups of one database :
    Logical backup makes a copy of the data in the database by dumping the content of each table, as well as object definitions for that same database (such as schemas, tables, indexes, views, privileges, triggers, and constraints).

    - Command :
    : pg_dump -F c <database> > dumpfile

    $ pg_dump -F c blog > dumpfile

    - If you want to dump directely SQL queries on the cli :
    $ pg_dump -v -d blog

    Note :: that pg_dump does not dump roles (such as users and groups) and tablespaces. Those two are only dumped by pg_dumpall; see the following recipes for more detailed descriptions.

    $ pg_dumpall

    - Get Hot logical backups of all databases on a PostgreSQL SERVER :
    $ pg_dumpall -g

    - Backups of database object definitions :

    The basic command to dump the definitions for every database of your PostgreSQL instance is as follows:
    $ pg_dumpall --schema-only > myscriptdump.sql

    This includes all objects, including roles, tablespaces, databases, schemas, tables, indexes, triggers, constraints, views, functions, ownerships, and privileges.
    If you want to dump PostgreSQL role definitions, use the following command:
       pg_dumpall --roles-only > myroles.sql
    If you want to dump PostgreSQL tablespace definitions, use the following command:
       pg_dumpall --tablespaces-only > mytablespaces.sql
    If you want to dump both roles and tablespaces, use the following command:
       pg_dumpall --globals-only > myglobals.sql

    You can also take advantage of a previously generated archive file (see the Hot logical backups of one database recipe) and generate a script file using pg_restore, as follows:
    $ pg_restore --schema-only mydumpfile > myscriptdump.sql

    - Data Directory of PostgreSQL :

    $ psql -d postgres
    $ SHOW data_directory;
    $ cd /usr/local/var/postgres

    + Create a Backup directory :
    $ mkdir ../standalone

    + Create the archive directory, as follows:
    $ mkdir ../standalone/archive

    + Start archiving with the following command:
    $ pg_receivewal -D ../standalone/archive/

    This command will not return, because pg_receivewal will run until interrupted. Therefore, you must open a new Terminal session to perform the next steps, starting with step 4.

    !! 2a and 3a are alternative to steps 2 and 3 (file-based archiving vs Streaming archiving)

    First, we described how to configure streaming archiving (steps 2 and 3). For file-based archiving, follow the alternate steps, 2a and 3a, instead, which are as follows:
    2a: Set archive_command. In postgresql.conf, you will need to add the following lines and restart the server, or just confirm that they are present:
                            $ SHOW config_file;
                            $ code /usr/local/var/postgres/postgresql.conf
                               archive_mode  =  on
                               archive_command  =  'test  !  -f  ../standalone/archiving_active  ||
                               cp  -i  %p  ../standalone/archive/%f'

    The last setting is only split into two lines for typesetting reasons; in postgresql.conf, you must keep it in a single line.
    You must also check that wal_level is set to either replica or logical, which is normally true as replica is the default setting.


    3a: Start archiving, as follows:
                         cd /usr/local/var/postgres
                         mkdir ../standalone/archive
                        touch ../standalone/archiving_active
    Irrespective of whether you have chosen streaming archiving or file-based archiving, you can now proceed with step 4.
    4. Define the name of the backup file. The following example includes time information in the filename:
                BACKUP_FILENAME=$(date '+%Y%m%d%H%M').tar
                echo $BACKUP_FILENAME
    5. Start the backup on blog database, as follows:
                psql -c "select pg_start_backup('standalone')" -d blog

    This step could take a while because PostgreSQL performs a checkpoint before returning to ensure that the data files copied in the next step include all of the latest data changes. See the Understanding and controlling crash recovery recipe from earlier in this chapter for more details about checkpoints.
    Depending on system configuration and workload, a checkpoint could take a long time, even several minutes. This time is part of the backup duration, which in turn affects the amount of WAL files needed for the backup; so it could be a good idea to reduce the duration of this checkpoint by issuing a CHECKPOINT command just before archiving is activated in step 3, and then by starting the backup in fast mode, as follows:
                psql -c "select pg_start_backup('standalone', fast := true)"
    fast mode means that the checkpoint included in pg_start_backup runs as quickly as possible, irrespective of its impact on the system; this should not be a problem because most of the shared buffers will have been written already by the CHECKPOINT command that was issued previously.
    6. Make a base backup—copy the data files (excluding the content of the pg_wal and pg_wal directories) using the following command:
                tar -cv \
                --exclude="pg_wal/*" --exclude="pg_replslot/*" \
                -f ../standalone/$BACKUP_FILENAME *

    7. Stop the backup, as follows:
                   psql -c "select pg_stop_backup(), current_timestamp" -d blog
    8. If you have followed steps 2 and step 3 (for example, if you are using streaming archiving), stop archiving by hitting Ctrl + C in the Terminal session where pg_receivewal is running:
    8a: Alternatively, if you have chosen steps 2a and 3a (for example, file-based archiving), enter the standalone directory and stop archiving, as follows:
                           rm ../standalone/archiving_active
    9. Add the archived files to the standalone backup, as follows: cd ../standalone
                   tar -rf $BACKUP_FILENAME archive
    10. Write a recovery.conf file to recover with:
    echo "restore_command = 'cp archive/%f %p'" > recovery.conf
                   echo "recovery_end_command = 'rm -R archive' " >> recovery.conf
    11. Add recovery.conf to the tar archive, as follows: tar -rf $BACKUP_FILENAME recovery.conf
    12. Clean up:
                   rm -rf archive recovery.conf
    13. Store $BACKUP_FILENAME somewhere safe. A safe place is definitely not on the same server.
    This procedure ends with a file named $BACKUP_FILENAME in the standalone directory. It is imperative to remember to copy it somewhere safe. This file contains everything that you need to recover, including a recovery parameter file.


    - Restoring All databases :
    1. Restoring of all databases means simply restoring each individual database from each dump you took. Confirm that you have the correct backup before you restore:
               pg_restore --schema-only -v dumpfile | head | grep Started
    2. Reload the global objects from the script file, as follows:
                   psql -f myglobals.sql
    3. Reload all databases. Create the databases using parallel tasks to speed things up. This can be executed remotely without the need to
    transfer dump files between systems. Note that there is a separate dumpfile for each database:
                   pg_restore -C -d postgres -j 4 dumpfile

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

    $ sudo service postgresql stop
    $ sudo service postgresql start

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



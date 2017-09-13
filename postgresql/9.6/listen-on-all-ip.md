### PostgreSQL - Listen on All IPs

#### PostgreSQL version: 9.6
#### OS: Red Hat Enterprise Linux Server release 7.2 (Maipo)

By default, PostgreSQL is configured to listen to "localhost" on port 5432.
```bash
# netstat -atnp | grep 131069
tcp        0      0 127.0.0.1:5432            0.0.0.0:*               LISTEN      131069/postmaster
```

If you try to connect to the database from its public IP:
```bash
# psql -h 10.77.1.235 -U username
You will ge:
psql: could not connect to server: Connection refused
	Is the server running on host "10.77.1.235" and accepting
	TCP/IP connections on port 5432?
```

In order to open up the connection, you need to update "postgresql.conf" and "pg_hba.conf" files. In my case, I am running "postgresql-9.6", so two files are located at:
```bash
/var/lib/pgsql/9.6/data/postgresql.conf
    - To have postgresql listening on 0.0.0.0
/var/lib/pgsql/9.6/data/pg_hba.conf
    - To allow incoming connections
```

#### Configuring postgresql.conf
Open postgresql.conf file and update line
```bash
listen_addresses = 'localhost'                  # what IP address(es) to listen on
to
listen_addresses = '*'                  # what IP address(es) to listen on
```

#### Configuring pg_hba.conf
Open pg_hba.conf file and update line
```bash
host    all             all             127.0.0.1/32            ident
to
host    all             all             0.0.0.0/0               trust
```

Now restart the service:
```bash
#  systemctl restart postgresql-9.6.service
```

Try command "psql -h 10.77.1.235 -U username" again, you should be able to login.
```bash
# psql -h 10.77.1.235 -U username
psql (9.2.18, server 9.6.4)
WARNING: psql version 9.2, server version 9.6.
         Some psql features might not work.
Type "help" for help.

username=>
```
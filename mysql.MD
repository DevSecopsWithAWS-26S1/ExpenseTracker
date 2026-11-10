# MySQL database

MySQL stores the expense records used by the application. This guide installs the server, starts it, sets the root password, and checks that it is ready.

## Install and start MySQL

Install MySQL Server 8.0.x:

```sh
dnf install mysql-server -y
```

Enable the service at boot and start it now:

```sh
systemctl enable mysqld
systemctl start mysqld
```

Set a root password before using the database. The example uses `ExpenseApp@1`; choose and use the same password consistently if you change it.

```sh
mysql_secure_installation --set-root-pass ExpenseApp@1
```

## Check the database

The `mysql` client can connect to a remote server with:

```sh
mysql -h <host-address> -u root -p<password>
```

If the client and server are on the same host, start the client with:

```sh
mysql
```

At the MySQL prompt, list the available databases:

```sql
show databases;
```

After selecting a database, list its tables:

```sql
show tables;
```

To inspect the rows in a table:

```sql
select * from <table_name>;
```

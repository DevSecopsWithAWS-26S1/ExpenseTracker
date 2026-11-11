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
mysql_secure_installation 
```
* check the strength of password / set password / :
```
Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: n
Please set the password for root here.

New password:

Re-enter new password:
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y

 ... skipping.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : Y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!


```


## Check the database

The `mysql` client can connect to a  mysql-server with:

```sh
mysql  -u root -p<password>
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

To exit from mysql 

```sql
quit
```

# Backend service

The Node.js service handles expense API requests and stores them in MySQL. The steps below prepare the host, install the application, register it with systemd, and load its database schema.

## Install Node.js

The project uses Node.js 20. Check the available module streams, enable version 20, and install it:

```sh
dnf module list nodejs
dnf module disable nodejs -y
dnf module enable nodejs:20 -y
dnf install nodejs -y
```

Update the OpenSSH packages as well to help keep remote access working:

```sh
dnf update -y openssh openssh-server openssh-clients
```

## Prepare the application

Create a dedicated system account. It is used to run the service, not for interactive logins:

```sh
useradd expense
```

Keep the application files in `/app`:

```sh
mkdir /app
```

Download and unpack the backend archive:

```sh
curl -L -o /tmp/backend.zip https://github.com/DevSecopsWithAWS-26S1/Expenseproject/raw/refs/heads/main/expense-backend-v2.zip
cd /app
unzip /tmp/backend.zip
```

Install the Node.js packages listed in `package.json`:

```sh
cd /app
npm install
```

## Register the service

Create a systemd unit for the backend:

```sh
vim /etc/systemd/system/backend.service
```

Add the service configuration below. Replace `<MYSQL-SERVER-IPADDRESS>` with the database host's address.

```ini
[Unit]
Description=Expense Backend Service

[Service]
User=expense
Environment=DB_HOST="<MYSQL-SERVER-IPADDRESS>"
Environment=DB_PWD="ExpenseApp@1"
ExecStart=/bin/node /app/index.js
SyslogIdentifier=backend

[Install]
WantedBy=multi-user.target
```

The example database schema creates the application account with the shown password. If you change it, update both this setting and the schema.

Reload systemd, then start the backend and enable it to start at boot:

```sh
systemctl daemon-reload
systemctl start backend
systemctl enable backend
```

## Load the database schema

Install a MySQL client on the backend host:

```sh
dnf install mysql -y
```

Load the schema into the database server. Use the matching database root password:

```sh
mysql -h <MYSQL-SERVER-IPADDRESS> -uroot -pExpenseApp@1 < /app/schema/backend.sql
```

The schema grants the application account only the `SELECT`, `INSERT`, and `DELETE` permissions used by the API.

Restart the service after the schema is in place:

```sh
systemctl restart backend
```

## Delete one expense

The API can remove a single expense by its database ID. Send a `DELETE` request to `/transaction/<id>`, for example:

```sh
curl -X DELETE http://<BACKEND-SERVER-IPADDRESS>:8080/transaction/12
```

The API returns a not-found response if that ID does not exist. To delete every expense instead, send a `DELETE` request to `/transaction` with no ID.

# Frontend service

Nginx serves the static Expense Tracker page and forwards API requests to the backend. Set up the web host in the order shown below.

## Install and start Nginx

Install Nginx, enable it at boot, and start it now:

```sh
dnf install nginx -y
systemctl enable nginx
systemctl start nginx
```

The web tier listens on HTTP port `80` by default. Allow inbound TCP port `80` from the intended client networks in the host firewall and, when applicable, the cloud security group. Do not expose the backend port `8080` or MySQL port `3306` publicly; allow those ports only from the tier that needs to connect.

This guide configures HTTP only. It does not configure HTTPS, a TLS certificate, or port `443`; add TLS before using the site for production traffic over untrusted networks.

Open the site once in a browser to confirm that Nginx is responding with its default page.

Remove the default page files before copying the application:

```sh
rm -rf /usr/share/nginx/html/*
```

## Publish the frontend

Download the frontend archive and unpack it into Nginx's document directory:

```sh
curl -fL -o /tmp/frontend.zip https://github.com/DevSecopsWithAWS-26S1/Expenseproject/raw/blob/main/expense-frontend-v2.zip?raw=true
cd /usr/share/nginx/html
dnf install unzip -y
unzip /tmp/frontend.zip
```

Reload the site in the browser and check that the Expense Tracker page appears. You can add expenses, delete an individual record, or use **Delete all** to clear the records.

## Frontend archive

The frontend ZIP contains the static page files. Extract it directly to the Nginx document root as shown above. The page requests `/api/transaction`; configure the Nginx proxy below before using expense features.

## Forward API requests

Create an Nginx configuration for the application:

```sh
vi /etc/nginx/default.d/expense.conf
```

Add the following settings. Replace `localhost` with the backend host's IP address when Nginx and the backend run on separate machines.

```nginx
proxy_http_version 1.1;

location /api/ { proxy_pass http://localhost:8080/; }

location /health {
  stub_status on;
  access_log off;
}
```

Validate the configuration, then reload Nginx to apply it:

```sh
nginx -t
systemctl reload nginx
```

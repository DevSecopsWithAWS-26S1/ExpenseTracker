# Frontend service

Nginx serves the static Expense Tracker page and forwards API requests to the backend. Set up the web host in the order shown below.

## Install and start Nginx

Install Nginx, enable it at boot, and start it now:

```sh
dnf install nginx -y
systemctl enable nginx
systemctl start nginx
```

Open the site once in a browser to confirm that Nginx is responding with its default page.

Remove the default page files before copying the application:

```sh
rm -rf /usr/share/nginx/html/*
```

## Publish the frontend

Download the frontend archive and unpack it into Nginx's document directory:

```sh
curl -L -o /tmp/frontend.zip https://github.com/DevSecopsWithAWS-26S1/Expenseproject/raw/refs/heads/main/expense-frontend-v2.zip
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

Restart Nginx to apply the configuration:

```sh
systemctl restart nginx
```


over the network.

- `off` – Network connections are blocked by this SELinux policy.
- `on` – Network connections are allowed by this SELinux policy.

3\. Enable network connections for Nginx

```
sudo setsebool -P httpd_can_network_connect 1
```

Purpose: Enables the SELinux permission that allows Nginx to establish outbound network connections to backend services.

- `-P` – Makes the setting permanent, including after server reboots.
- `1` – Enables the permission.

Expected result:

```
httpd_can_network_connect --> on
```

Why is this required?

Nginx acts as a reverse proxy and forwards API requests from the frontend server to the backend Node.js application running on port `8080`. SELinux may block these connections by default. Enabling this permission allows Nginx to communicate with the backend without disabling SELinux.

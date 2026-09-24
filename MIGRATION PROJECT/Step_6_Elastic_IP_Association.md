Step 6: Elastic IP Association

## Objective

Assign a permanent public IPv4 address to the Sydney EC2 instance so that stopping and starting the instance no longer requires repeated IP changes in MongoDB Atlas, SSH commands, frontend configuration, or browser bookmarks.

The stable address assigned in this step is:

~~~text
13.55.22.202
~~~

## Why an Elastic IP was needed

The automatically assigned EC2 public IP changed whenever the instance was stopped and started. That required updates to:

- the MongoDB Atlas IP access list;
- the backend FRONTEND_URL value;
- the Windows SSH command; and
- the URL used to open JerseyHub.

An Elastic IP remains assigned until it is disassociated or released.

## 1. Allocate an Elastic IP

In AWS Console:

1. Select the Sydney region, ap-southeast-2.
2. Open EC2.
3. Open Network and Security, then Elastic IPs.
4. Click Allocate Elastic IP address.
5. Keep Amazon's pool of IPv4 addresses selected.
6. Keep the network border group as ap-southeast-2.
7. Add the optional tag:
   - Key: Name
   - Value: jerseyhub-devops-eip
8. Click Allocate.

Allocated address:

~~~text
13.55.22.202
~~~

Do not select AWS Global Accelerator. It is not needed for this deployment.

![Allocate Elastic IP](docs/images/step5-elastic-ip-allocate.png)

## 2. Associate the address with EC2

Select the new Elastic IP and choose Associate Elastic IP address.

Use:

| Field | Value |
|---|---|
| Resource type | Instance |
| Instance ID | i-0a9d94d76e09251e3 |
| Private IP | 172.31.33.53 |
| Elastic IP | 13.55.22.202 |
| Reassociation | Enabled |

Then click Associate.

![Allocated Elastic IP](docs/images/step5-elastic-ip-details.png)

![Associate Elastic IP](docs/images/step5-elastic-ip-associate.png)

The active SSH connection may disconnect when the public address changes. Association does not reboot EC2, and the Docker containers remain running.

## 3. Reconnect through SSH

From Windows PowerShell or Command Prompt:

~~~powershell
ssh -i "YOUR-KEY.pem" ubuntu@13.55.22.202
~~~

Verify the public IP:

~~~bash
curl -4 https://checkip.amazonaws.com
~~~

Verified result:

~~~text
13.55.22.202
~~~

Check the application containers:

~~~bash
cd ~/jerseyhub-devops
docker compose ps
~~~

Both services remained Up:

- jerseyhub-devops-backend-1
- jerseyhub-devops-frontend-1

## 4. Update MongoDB Atlas

Open MongoDB Atlas and navigate to:

~~~text
Database and Network Access -> IP Access List
~~~

Add:

~~~text
13.55.22.202/32
~~~

Recommended comment:

~~~text
JerseyHub EC2 Elastic IP Sydney
~~~

Wait until the entry becomes Active. After the backend successfully connects from the Elastic IP, remove the previous dynamic entry:

~~~text
15.135.221.156/32
~~~

The Atlas IP access list no longer needs to be updated after ordinary EC2 stop and start cycles.

## 5. Update the backend environment

On EC2:

~~~bash
cd ~/jerseyhub-devops

sed -i 's|^FRONTEND_URL=.*|FRONTEND_URL=http://13.55.22.202|' backend/.env

grep '^FRONTEND_URL=' backend/.env
~~~

Expected value:

~~~text
FRONTEND_URL=http://13.55.22.202
~~~

The backend .env file must remain excluded from Git because it contains secrets.

## 6. Recreate the backend container

A normal restart does not reload values supplied through env_file. Recreate the backend service:

~~~bash
docker compose up -d --force-recreate backend
sleep 10

docker compose ps
docker compose logs --tail 30 backend
~~~

Verified logs:

~~~text
MongoDB Atlas Connected
Server running on port 2987
~~~

The Gmail login warning was expected because temporary test credentials were still configured. It did not stop the application.

## 7. Test the stable address

Test the frontend:

~~~bash
curl -I http://13.55.22.202/
~~~

Verified result:

~~~text
HTTP/1.1 200 OK
Server: nginx
~~~

Test Nginx to Express proxying:

~~~bash
curl -i http://13.55.22.202/api/
~~~

Verified response:

~~~text
Football Jersey Store API Running
~~~

The application is now available at:

~~~text
http://13.55.22.202
~~~

## Result

The EC2 instance now has a stable public address. Normal stop and start cycles no longer require changing the Atlas allowlist, SSH destination, backend FRONTEND_URL, or website URL.

The Elastic IP must not be released while it is being used. AWS public IPv4 charges may continue while the address is allocated.

## Next step

Configure a free hostname and HTTPS:

~~~text
13-55-22-202.sslip.io -> 13.55.22.202 -> Nginx -> Express
~~~

HTTPS will remove the browser's Not secure warning and allow production secure session cookies to function correctly.

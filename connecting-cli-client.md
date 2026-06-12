---

copyright:
  years: 2026
lastupdated: "2026-06-12"

keywords: redis, databases, update, client, pub/sub

subcollection: databases-for-redis-gen2

---

{{site.data.keyword.attribute-definition-list}}

# Connecting through the command-line interface (CLI)
{: #connecting-cli-client}

[Gen 2]{: tag-purple}

You can access your Redis database directly from a command-line interface (CLI). The CLI allows for direct interaction and monitoring of the data structures that are created within the database. It is also useful for administering and monitoring the keyspace and performance, installing and modifying scripts, and other management activities.

## Create a secure connection on a Virtual Server Instance (VSI) using a Virtual Private Endpoint gateway (VPE)
{: #vpe-connection}

Complete these steps to build a secure compliant network with private endpoints for database operations, which is recommended for production usage.

### Step 1. Create an IBM Cloud Virtual Private Cloud (VPC)
{: #create-vpc}

Set up a [Virtual Private Cloud](https://cloud.ibm.com/infrastructure/network/vpcs){: external} in your region. Select **Allow SSH** for the Default security group setting.

Keep resources in the same region as your deployment to avoid issues.
{: note}

### Step 2. Create a Virtual Server Instance (VSI) in the VPC
{: #create-vsi}

Create a [VSI](https://cloud.ibm.com/infrastructure/compute/vs){: external} in the same region, select your VPC, and choose Ubuntu Linux (size 1 GB) for the operating system. (You can use the smallest profile.) Then select the SSH key you created in the previous step.

You must select Ubuntu Linux for this tutorial. Other images can cause errors.
{: note}

### Step 3. Create an SSH key
{: #create-ssh-key}

Create an [SSH key](https://cloud.ibm.com/infrastructure/compute/sshKeys){: external} in the same region as the VPC.

When your key is ready, download and move it to the `.ssh` directory on your local machine to follow best practices for secure SSH key management. Make sure you save the key with the `.prv` extension.

Next, update the key's permissions to make it read-only for the file owner. On Unix-like systems such as macOS, run the following command:

```sh
chmod 400 <COPY_LOCAL_LOCATION_OF_THE_SSH_KEY>
```
{: pre}

You can also create the SSH while creating the VSI. This helps to avoid unnecessary SSH key errors.
{: note}

### Step 4. Reserve a floating IP for your VSI
{: #reserve-floating-ip}

Reserve a [floating IP address](https://cloud.ibm.com/infrastructure/network/floatingIPs){: external} and make sure the correct region and zone is selected. Bind it to the VSI created in the previous step.

### Step 5. Add your IP to the security group inbound rule of the VSI
{: #add-ip-security-group}

Run the following command to get the IP:

```sh
curl ipinfo.io/ip
```
{: pre}

You can find the security group in the VPC details. Go to your VPC details page, follow the Default security group link, and navigate to **rules > Inbound rules**.

Create the rule with your IP address. Set the port range: port min 22 and port max 22. Select the source type as IP or CIDR and enter your IP. Leave other details as the defaults. Click **Save**.

### Step 6. Log in to your VSI
{: #login-vsi}

In your terminal, SSH in to your VSI with the following command:

If you saved the private SSH key in a different directory, replace the file path in the command accordingly.

```sh
ssh -i ~/.ssh/<SSH_file_name>.prv ubuntu@<floating IP>
```
{: pre}

For example:

```sh
ssh -i /Users/username/Downloads/redis-ssh-key.prv ubuntu@169.63.188.229
```
{: pre}

```text
Welcome to Ubuntu 24.04.4 LTS (GNU/Linux 6.8.0-1049-ibm x86_64)
```
{: screen}

Your local terminal session should now be connected to your virtual server. Continue using this session for the following steps.

If you get a timeout error connecting to VSI, check for the IP `curl ipinfo.io/ip`. If the value has rotated, update your security group rule of your respective VPC.
{: note}

### Step 7. Install redis-cli to your VSI
{: #install-redis-cli}

Install redis-cli with the following commands:

```sh
sudo apt install redis-tools
```
{: pre}

Next, you can verify the installation:

```sh
redis-cli --version
redis-cli ping
```
{: pre}

### Step 8. Create a Virtual Private Endpoint (VPE) gateway
{: #create-vpe}

Create a [VPE](https://cloud.ibm.com/infrastructure/network/endpointGateways){: external}, select the correct region, VPC. Then in the Cloud service offering dropdown, enable **Databases for Redis**, and choose your database instance that this private gateway is needed. Other settings can remain as the defaults.

### Step 9. Create user in your Redis instance
{: #create-user}

Create the user from the service-credentials tab in the Manager or Writer role. Make sure you save the service credential details in a file because the credentials are one-time view basis.

### Step 10. Connect to the database instance with VPE
{: #connect-vpe}

You can find the hostname in the **overview page > service endpoint panel**.

Replace the user and password placeholder with your database credentials:

```sh
redis-cli -h <hostname> -p 6379 --user <USERNAME> -a <PASSWORD> --tls --sni <hostname>
```
{: pre}

Database instances with private endpoints are reachable from any account within the private network and access to each instance requires authentication. To restrict this access to specific IP addresses or ranges of IP addresses, configure [Context-based restrictions](/docs/cloud-databases-gen2?topic=cloud-databases-gen2-cbr&interface=ui).
{: note}

## Next steps
{: #next-steps-cli}

* [Learn about Redis features](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-redis-features)
* [Connect an external application](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-external-app)
* [Connect an IBM Cloud application](/docs/databases-for-redis-gen2?topic=databases-for-redis-gen2-ibmcloud-app)

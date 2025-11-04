# Elasticsearch 8.x Installation Guide (Ubuntu/Debian)

This guide walks you through installing and configuring **Elasticsearch 8.x** on Ubuntu/Debian systems using the official Elastic APT repository.  
All steps are based on the official [Elastic documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html).

---

## 1. System Update and Required Packages

Update your package index and install the required dependencies:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install apt-transport-https ca-certificates curl -y
```

---

## 2. Add the Official Elastic APT Repository

### Step 1: Import the Elastic GPG Key

```bash
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic-archive-keyring.gpg
```

### Step 2: Add the Elastic Repository

```bash
echo "deb [signed-by=/usr/share/keyrings/elastic-archive-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
sudo apt update
```

This is the **official** method recommended by Elastic for Debian-based systems.

---

## 3. Install Elasticsearch

Install Elasticsearch using APT:

```bash
sudo apt install elasticsearch -y
```

After installation, enable and start the Elasticsearch service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now elasticsearch.service
sudo systemctl status elasticsearch.service
```

---

## 4. Verify Installation

To verify if Elasticsearch is running, execute:

```bash
curl -k https://localhost:9200
```

You should see a JSON response with cluster information.

---

## 5. Basic Configuration

Edit the main configuration file:

```bash
sudo nano /etc/elasticsearch/elasticsearch.yml
```

Example configuration:

```yaml
cluster.name: my-cluster
node.name: node-1
network.host: 0.0.0.0       # Allow external access (use only for development)
http.port: 9200
```

### JVM Memory Settings
Edit the JVM options file (adjust for your system resources):

```bash
sudo nano /etc/elasticsearch/jvm.options.d/jvm.options
```

Example:

```
-Xms2g
-Xmx2g
```

> **Note:**  
> Use `network.host: 0.0.0.0` **only for development/testing**.  
> In production, always bind to a specific IP and use proper security (firewall, TLS, and authentication).

---

## 6. Security (Elasticsearch 8.x Default Behavior)

Starting with **Elasticsearch 8.x**, **X-Pack Security** is enabled by default.  
During the initial startup, Elasticsearch may auto-generate:
- Security certificates
- A bootstrap password for the built-in `elastic` superuser

If you installed manually, follow the recommended password setup procedure below.

---

## 7. Reset or Set the `elastic` User Password

Elasticsearch provides the `elasticsearch-reset-password` utility for managing built-in user passwords.

### Interactive password reset:

```bash
sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password --interactive -u elastic
```

### Direct password reset (non-interactive):

```bash
sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
```

Follow the prompts to set or reset the password.

> If your cluster is new and initial passwords have not been set,  
> you can also use (deprecated but available):
>
> ```bash
> sudo /usr/share/elasticsearch/bin/elasticsearch-setup-passwords interactive
> ```

---

## 8. Test Authentication

Use your new password to verify authentication:

```bash
curl -u elastic:YOUR_PASSWORD -k https://localhost:9200
```

If successful, you’ll receive the cluster information in JSON format.

---

## 9. Troubleshooting

If `elasticsearch-reset-password` is not found or fails:
- Make sure the Elasticsearch service is **running**.
- Run the command from inside `/usr/share/elasticsearch/bin/`.

Older versions used:
```bash
bin/elasticsearch-setup-passwords auto
```
but this method is **deprecated in 8.x**.

---

## References

- [Elastic Official Docs – Install Elasticsearch with Debian Package](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html)
- [Elastic Security Overview](https://www.elastic.co/guide/en/elasticsearch/reference/current/security-settings.html)
- [X-Pack Features](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-xpack.html)

---

## Author

**Jagadish Tripathy**  
Cybersecurity Engineer | SOC & VAPT Specialist  
*Documentation written for secure Elasticsearch setup (development-ready configuration).*

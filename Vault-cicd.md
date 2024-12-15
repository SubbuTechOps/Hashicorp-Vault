
# **Setting Up HashiCorp Vault for Storing Secrets and Integrating with CI/CD Pipelines**

### **Step 1: Install HashiCorp Vault**

#### Prerequisites:

- A server with at least 2GB RAM and 2 CPUs.
- OS: Ubuntu, CentOS, or another Linux distribution.
- Open ports: 8200 (Vault HTTP API) and 8201 (Cluster communication).

#### Steps to Install:

1. **Download Vault binary:**
    
    ```bash
    curl -fsSL https://apt.releases.hashicorp.com/gpg | gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
    sudo apt update && sudo apt install vault -y
    ```
    
2. **Verify Installation:**
    
    ```bash
    vault --version
    ```
    
3. **Enable Vault as a Service (Optional):**
Create a file `/etc/vault.d/vault.hcl` with basic configuration:
    
    ```hcl
    storage "file" {
        path = "/opt/vault/data"
    }
    
    listener "tcp" {
        address = "0.0.0.0:8200"
        tls_disable = 1
    }
    
    ui = true
    ```
    
    Enable and start the Vault service:
    
    ```bash
    sudo systemctl enable vault
    sudo systemctl start vault
    ```
    

---

### **Step 2: Initialize and Unseal Vault**

1. **Initialize Vault:**
    
    ```bash
    vault operator init
    ```
    
    - Vault generates **5 unseal keys** and a **root token**.
    - Save these securely.
2. **Unseal Vault:**
Use three of the five unseal keys to unseal Vault:
    
    ```bash
    vault operator unseal <UNSEAL_KEY_1>
    vault operator unseal <UNSEAL_KEY_2>
    vault operator unseal <UNSEAL_KEY_3>
    ```
    
3. **Log in to Vault:**
Use the root token to log in:
    
    ```bash
    vault login <ROOT_TOKEN>
    ```
    

---

### **Step 3: Enable a Secrets Engine**

1. **Enable KV Secrets Engine (v2):**
    
    ```bash
    vault secrets enable -path=secret kv-v2
    ```
    
2. **Verify Secrets Engine:**
    
    ```bash
    vault secrets list
    ```
    

---

### **Step 4: Store Secrets in Vault**

1. **Add a Secret:**
Store AWS credentials as an example:
    
    ```bash
    vault kv put secret/aws access_key_id=AKIA1234 secret_access_key=abcd1234xyz
    ```
    
2. **View the Secret:**
    
    ```bash
    vault kv get secret/aws
    ```
    

---

### **Step 5: Configure Vault Authentication**

1. **Enable AppRole Authentication:**
    
    ```bash
    vault auth enable approle
    ```
    
2. **Create a Policy for Access:**
Create a policy file, e.g., `aws-policy.hcl`:
    
    ```hcl
    path "secret/data/aws" {
        capabilities = ["read"]
    }
    ```
    
    Apply the policy:
    
    ```bash
    vault policy write aws-policy aws-policy.hcl
    ```
    
3. **Create an AppRole:**
    
    ```bash
    vault write auth/approle/role/cicd-role \
        token_policies="aws-policy" \
        secret_id_ttl=24h \
        token_ttl=1h \
        token_max_ttl=4h
    ```
    
4. **Retrieve Role ID and Secret ID:**
    - Fetch the **Role ID**:
        ```bash
        vault read auth/approle/role/cicd-role/role-id
        ```
    
    - Generate a **Secret ID**:
        ```bash
        vault write -f auth/approle/role/cicd-role/secret-id
        ```

5. **Test AppRole Authentication:**
    - Use the Role ID and Secret ID to authenticate:
        
        ```bash
        vault write auth/approle/login role_id="<ROLE_ID>" secret_id="<SECRET_ID>"
        ```
        
    - Vault will return a token. Use it for accessing secrets.

---

### **Step 6: Integrate Vault with Jenkins Pipeline**

1. **Install HashiCorp Vault Plugin in Jenkins**:
   - Go to Jenkins Dashboard > Manage Jenkins > Plugins > Install "HashiCorp Vault" and "HashiCorp Vault Pipeline" plugins.

2. **Add Vault Configuration in Jenkins**:
   - Go to Jenkins Dashboard > Manage Jenkins > Configure System.
   - Add Vault server URL and authentication method (e.g., AppRole).

3. **Jenkins Pipeline Script to Fetch Secrets**:
   Use the `withVault` step to retrieve secrets dynamically.

   **Example Jenkins Pipeline Code**:
   ```groovy
   pipeline {
       agent any
       environment {
           VAULT_ADDR = 'http://<VAULT_SERVER>:8200'
       }
       stages {
           stage('Fetch Secrets') {
               steps {
                   withVault([vaultSecrets: [
                       [path: 'secret/aws', secretValues: [
                           [envVar: 'AWS_ACCESS_KEY_ID', vaultKey: 'access_key_id'],
                           [envVar: 'AWS_SECRET_ACCESS_KEY', vaultKey: 'secret_access_key']
                       ]]
                   ]]) {
                       sh 'echo AWS Access Key: $AWS_ACCESS_KEY_ID'
                   }
               }
           }
       }
   }
   ```

---

## **Conclusion**
By following these steps, you can set up HashiCorp Vault, securely store secrets, configure authentication, and integrate it seamlessly into a Jenkins CI/CD pipeline. This ensures secrets are dynamically fetched, securely managed, and never hardcoded into your pipeline scripts.


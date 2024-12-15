# Essential HashiCorp Vault Commands

This document provides a comprehensive list of essential HashiCorp Vault commands for setup, management, and day-to-day usage. These commands are grouped based on their functionalities.

---

## 1. **Vault Setup and Initialization**

- **Start the Vault server in Dev Mode**:
  ```bash
  vault server -dev
  ```
  
- **Initialize Vault**:
  ```bash
  vault operator init
  ```
  
- **Unseal Vault (with keys)**:
  ```bash
  vault operator unseal <unseal-key>
  ```
  
- **Check Vault Status**:
  ```bash
  vault status
  ```

- **Seal the Vault manually**:
  ```bash
  vault operator seal
  ```

- **Rekey Vault (generate new unseal keys)**:
  ```bash
  vault operator rekey
  ```

---

## 2. **Authentication**

- **Login to Vault (using Token)**:
  ```bash
  vault login <root-token>
  ```

- **Enable AppRole Authentication**:
  ```bash
  vault auth enable approle
  ```

- **List Authentication Methods**:
  ```bash
  vault auth list
  ```

- **Create AppRole**:
  ```bash
  vault write auth/approle/role/<role-name>
  ```

- **Fetch Role ID for AppRole**:
  ```bash
  vault read auth/approle/role/<role-name>/role-id
  ```

- **Fetch Secret ID for AppRole**:
  ```bash
  vault write -f auth/approle/role/<role-name>/secret-id
  ```

---

## 3. **Secrets Management**

### **KV Secrets Engine (Key-Value Store)**

- **Enable KV Secrets Engine (Version 2)**:
  ```bash
  vault secrets enable -path=kv kv-v2
  ```

- **Write a Secret to KV Engine**:
  ```bash
  vault kv put kv/<secret-path> key1=value1 key2=value2
  ```

- **Read a Secret**:
  ```bash
  vault kv get kv/<secret-path>
  ```

- **List Secrets**:
  ```bash
  vault kv list kv/<path>
  ```

- **Delete a Secret**:
  ```bash
  vault kv delete kv/<secret-path>
  ```

- **Destroy a Secret Version**:
  ```bash
  vault kv destroy kv/<secret-path> -versions=1
  ```

- **Undelete a Secret**:
  ```bash
  vault kv undelete kv/<secret-path> -versions=1
  ```

### **Dynamic Secrets (e.g., AWS)**

- **Enable AWS Secrets Engine**:
  ```bash
  vault secrets enable aws
  ```

- **Configure AWS Credentials**:
  ```bash
  vault write aws/config/root access_key=<AWS_ACCESS_KEY> secret_key=<AWS_SECRET_KEY>
  ```

- **Create AWS IAM Role (Dynamic Credentials)**:
  ```bash
  vault write aws/roles/<role-name> policy=arn:aws:iam::aws:policy/AdministratorAccess
  ```

- **Generate AWS Credentials Dynamically**:
  ```bash
  vault read aws/creds/<role-name>
  ```

---

## 4. **Policy Management**

- **Create a Policy (using HCL file)**:
  ```bash
  vault policy write <policy-name> <policy-file.hcl>
  ```

- **List All Policies**:
  ```bash
  vault policy list
  ```

- **Read a Specific Policy**:
  ```bash
  vault policy read <policy-name>
  ```

- **Delete a Policy**:
  ```bash
  vault policy delete <policy-name>
  ```

---

## 5. **Token Management**

- **Create a Token**:
  ```bash
  vault token create
  ```

- **Lookup Information About a Token**:
  ```bash
  vault token lookup <token>
  ```

- **Renew a Token**:
  ```bash
  vault token renew <token>
  ```

- **Revoke a Token**:
  ```bash
  vault token revoke <token>
  ```

- **Revoke All Tokens Associated with a Lease**:
  ```bash
  vault lease revoke <lease-id>
  ```

---

## 6. **Lease Management**

- **List Leases**:
  ```bash
  vault list sys/leases
  ```

- **Renew a Lease**:
  ```bash
  vault lease renew <lease-id>
  ```

- **Revoke a Lease**:
  ```bash
  vault lease revoke <lease-id>
  ```

---

## 7. **Audit Logs**

- **Enable an Audit Backend**:
  ```bash
  vault audit enable file file_path=/var/log/vault_audit.log
  ```

- **List Enabled Audit Devices**:
  ```bash
  vault audit list
  ```

- **Disable an Audit Backend**:
  ```bash
  vault audit disable <audit-device>
  ```

---

## 8. **Vault Server Maintenance**

- **Reload the Vault Server**:
  ```bash
  vault reload
  ```

- **Check Vault Configuration**:
  ```bash
  vault server -config=<config-file.hcl> -check
  ```

- **Upgrade Vault** (Replace binaries and restart Vault):
  ```bash
  systemctl restart vault
  ```

---

## 9. **Monitoring and Debugging**

- **Enable Telemetry Endpoint**:
  ```bash
  vault secrets enable -path=telemetry telemetry
  ```

- **Check Metrics**:
  ```bash
  curl http://<vault-server>:8200/v1/sys/metrics
  ```

- **Enable Debug Logs**:
  Update configuration:
  ```hcl
  log_level = "debug"
  ```

---

## 10. **Miscellaneous**

- **Enable a Secrets Engine**:
  ```bash
  vault secrets enable <engine-name>
  ```

- **Disable a Secrets Engine**:
  ```bash
  vault secrets disable <path>
  ```

- **Enable Audit Device**:
  ```bash
  vault audit enable <audit-device>
  ```

- **Check Vault Version**:
  ```bash
  vault version
  ```

---

## Conclusion
These commands provide a solid foundation for managing HashiCorp Vault, from initialization and authentication to secrets management and audit logging. Regular practice and usage of these commands will help streamline secure secret management workflows.                        


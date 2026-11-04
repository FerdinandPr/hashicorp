#HashiCorp

#Volume anlegen
podman volume create vault-data

#Container anstarten

```bash
$cfg = '{"storage":{"file":{"path":"/vault/file"}},"listener":{"tcp":{"address":"0.0.0.0:8200","tls_disable":true}},"ui":true,"api_addr":"http://127.0.0.1:8200"}'

# Nur bei Windows PowerShell 5.1 / PowerShell älter als 7.3: Anführungszeichen escapen
if ($PSVersionTable.PSVersion -lt [version]'7.3') { $cfg = $cfg.Replace('"','\"') }

podman run -d --name vault --cap-add=IPC_LOCK -p 8200:8200 `
  -v vault-data:/vault/file `
  -e VAULT_LOCAL_CONFIG=$cfg `
  docker.io/hashicorp/vault:latest server

  ```bash

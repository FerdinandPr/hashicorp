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
```

# Logs püfen

```bash
podman logs vault

==> Vault server configuration:

Administrative Namespace: 
             Api Address: http://127.0.0.1:8200
                     Cgo: disabled
         Cluster Address: https://127.0.0.1:8201
   Environment Variables: HOME, HOSTNAME, NAME, PATH, PWD, SHLVL, VAULT_LOCAL_CONFIG, container
              Go Version: go1.27.3
              Listener 1: tcp (addr: "0.0.0.0:8200", cluster address: "0.0.0.0:8201", disable_request_limiter: "false", max_json_array_element_count: "10000", max_json_depth: "300", max_json_object_entry_count: "10000", max_json_string_value_length: "1048576", max_request_duration: "1m30s", max_request_size: "33554432", tls: "disabled")
               Log Level: 
                   Mlock: supported: true, enabled: true
      Operator Namespace: 
           Recovery Mode: false
                 Storage: file
                 Version: Vault v2.2.1, built 2026-10-22T16:27:49Z
             Version Sha: 0e7c41b936218bd2401ab0f891c93bec10e2e8e4

==> Vault server started! Log data will stream in below:

2026-11-04T15:20:54.219Z [INFO]  proxy environment: http_proxy="" https_proxy="" no_proxy=""
2026-11-04T15:20:54.219Z [INFO]  incrementing seal generation: generation=1
2026-11-04T15:20:54.406Z [INFO]  core: Initializing version history cache for core
2026-11-04T15:20:54.406Z [INFO]  events: Starting event system
```
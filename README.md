#HashiCorp

## Volume anlegen
podman volume create vault-data

## Container anstarten

```bash
$cfg = '{"storage":{"file":{"path":"/vault/file"}},"listener":{"tcp":{"address":"0.0.0.0:8200","tls_disable":true}},"ui":true,"api_addr":"http://127.0.0.1:8200"}'

# Nur bei Windows PowerShell 5.1 / PowerShell älter als 7.3: Anführungszeichen escapen
if ($PSVersionTable.PSVersion -lt [version]'7.3') { $cfg = $cfg.Replace('"','\"') }

podman run -d --name vault --cap-add=IPC_LOCK -p 8200:8200 `
  -v vault-data:/vault/file `
  -e VAULT_LOCAL_CONFIG=$cfg `
  docker.io/hashicorp/vault:latest server
```

## Logs püfen

```bash
podman logs vault
```

```bash
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

## Function in Powershell definieren (nur für Session gültig)
```bash
function vlt { podman exec -i -e VAULT_ADDR=http://127.0.0.1:8200 -e VAULT_TOKEN=$env:VT vault vault @args }
```
## Initialisieren (einmalig)
```bash
vlt operator init -key-shares=1 -key-threshold=1
```
```bash
Unseal Key 1: i1vOd0hg/BFoW3FyGzTBPG9ZIPzb5G27wBb/1UZixiE=

Initial Root Token: hvs.7KijK3RQycCGsE3XVckvRaWE

Vault initialized with 1 key shares and a key threshold of 1. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 1 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated root key. Without at least 1 keys to
reconstruct the root key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault operator rekey" for more information.
```

## Entsperren (unseal)

```bash
vlt operator unseal i1vOd0hg/BFoW3FyGzTBPG9ZIPzb5G27wBb/1UZixiE=
```
```bash
unseal i1vOd0hg/BFoW3FyGzTBPG9ZIPzb5G27wBb/1UZixiE=
Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false
Total Shares    1
Threshold       1
Version         2.2.1
Build Date      2026-10-22T16:27:49Z
Storage Type    file
Cluster Name    vault-cluster-ee156450
Cluster ID      33ffe4f3-b939-4d67-0b19-04eab4f2d742
HA Enabled      false
```
## Root Token setzen
```bash
$env:VT = "hvs.7KijK3RQycCGsE3XVckvRaWE"
```
## KV-Secrets-Engine aktivieren
```bash
vlt secrets enable -path=secret kv-v2
```
```bash
vlt secrets enable -path=secret kv-v2
```

## userpass aktivieren
```bash
vlt auth enable userpass
```
```bash
vlt auth enable userpass
```
## Policy anlegen
```bash
@'
path "secret/data/*" {
  capabilities = ["create", "read", "update", "delete", "list"]
}
path "secret/metadata/*" {
  capabilities = ["list", "read", "delete"]
}
'@ | vlt policy write meine-policy -
```




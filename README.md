# hashicorp

##Anstarten
podman compose up -d

#Initialisieren
podman exec -it vault_server vault operator init -key-shares=1 -key-threshold=1

Kopiere dir den Unseal Key und das Initial Root Token aus der Terminal-Ausgabe.

Entsperre Vault:

podman exec -it vault_server vault operator unseal <DEIN_UNSEAL_KEY>




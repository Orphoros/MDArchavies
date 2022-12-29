# Hashicorp Vault

## File system setup

```bash
mkdir -p volumes/{config,file,logs}
```

## Docker compose file


```bash
touch docker-compose.yml
```

```yaml
version: '2'
services:
  vault:
    image: vault
    container_name: vault-server
    ports:
      - "8200:8200"
    networks:
      bridge:
	ipv4_address: 172.19.0.10
    restart: always
    volumes:
      - ./volumes/logs:/vault/logs
      - ./volumes/file:/vault/file
      - ./volumes/config:/vault/config
    cap_add:
      - IPC_LOCK
    entrypoint: vault server -config=/vault/config/vault.hcl
networks:
  bridge:
    name: microservices
    external: true
```

## GGP

### Get GPG key for Vault for encryption

```bash
gpg --export 5E0685D9 | base64 > public.asc
```

### Get the unlock key

```bash 
echo "encryptedbase64texthere" | base64 --decode | gpg -dq
```

## Policies

### Default admin hcl policy
    
```hcl  
# Read system health check
path "sys/health"
{
  capabilities = ["read", "sudo"]
}

# Access to tools
path "sys/tools"
{
  capabilities = ["read", "update"]
}

# Access to rewrap
path "sys/wrapping/rewrap"
{
  capabilities = ["read", "update"]
}

# Create and manage ACL policies broadly across Vault

# List existing policies
path "sys/policies/acl"
{
  capabilities = ["list"]
}

# Create and manage ACL policies
path "sys/policies/acl/*"
{
  capabilities = ["create", "read", "update", "delete", "list", "sudo"]
}

# Enable and manage authentication methods broadly across Vault

# Manage auth methods broadly across Vault
path "auth/*"
{
  capabilities = ["create", "read", "update", "delete", "list", "sudo"]
}

# Create, update, and delete auth methods
path "sys/auth/*"
{
  capabilities = ["create", "update", "delete", "sudo"]
}

# List auth methods
path "sys/auth"
{
  capabilities = ["read"]
}

# Enable and manage the key/value secrets engine at `secret/` path

# List, create, update, and delete key/value secrets
path "secret/*"
{
  capabilities = ["create", "read", "update", "delete", "list", "sudo"]
}

# Manage secrets engines
path "sys/mounts/*"
{
  capabilities = ["create", "read", "update", "delete", "list", "sudo"]
}

# List existing secrets engines.
path "sys/mounts"
{
  capabilities = ["read"]
}
```
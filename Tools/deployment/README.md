# Aerie Deployment

This repository deploys the Aerie SS14 server from GitHub Actions on every push to
`master`.

The workflow packages `release/SS14.Server_linux-x64.zip`, copies it to the VM as
`aerie-deploy`, and activates it with `/usr/local/sbin/aerie-deploy`.

## GitHub Secrets

The VM has already been configured to accept the deploy key at:

```text
/home/crow/.ssh/aerie_cd_ed25519
```

Add these repository secrets:

```shell
gh secret set AERIE_VM_HOST --body 35.223.83.245
gh secret set AERIE_VM_PORT --body 22
gh secret set AERIE_VM_SSH_KEY < ~/.ssh/aerie_cd_ed25519
```

`AERIE_VM_PORT` is optional because the workflow defaults to port `22`.

## VM Layout

```text
/opt/aerie/incoming      uploaded release zips
/opt/aerie/releases      unpacked immutable releases
/opt/aerie/current       symlink to the active release
/var/lib/aerie           persistent server state
/etc/aerie/server_config.toml
```

The server runs as the unprivileged `aerie` system user through
`aerie.service`.

## Firewall

The GCP firewall must allow public SS14 traffic:

```shell
gcloud compute firewall-rules create allow-aerie-ss14 \
  --project=beckwith-family \
  --direction=INGRESS \
  --action=ALLOW \
  --rules=tcp:1212,udp:1212 \
  --source-ranges=0.0.0.0/0 \
  --description="Allow Space Station 14 traffic to Aerie"
```

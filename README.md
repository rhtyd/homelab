# homelab

IaaS setup: https://rohityadav.cloud/blog/cloudstack-rpi4-kvm/

DDNS/domain: x32.in (private), yadav.cloud (public)

Nodes:
- cloudpi.x32.in: 4G 4C
- pikvm1.x32.in: 8G, 4C
- pikvm2.x32.in: 8G, 4C
- 2xrpi2 (deprecated)

cloudpi.x32.in services:
- cloudstack-management + MySQL
- ddns (cloudflare)
- apache2 (file sharing, ssl/letsencrypt + reverse proxy)
- nfs (500G T5)
- munin (monitoring)

TODO: (things I don't need :D ) https://www.reddit.com/r/homelab/wiki/software
- Homer dashboard?
- https://gitea.io
- jenkins or other CI?
- mbx + management UI
- Wireguard/tailscale
- Grafana?
- pihole
- ghost/blog
- mediawiki/docuwiki
- https://www.home-assistant.io/
- https://nextcloud.com
- eth/mining?
- pfsense?
- mqtt?
- IP sec-cams?
- deluge + plex? https://jellyfin.org? https://emby.media? couchpotato
- Experiment: django/postgres app

To play with:
- https://www.ovirt.org/
- https://www.proxmox.com/en/proxmox-ve
- https://www.nutanix.com/products/community-edition
- http://www.openvswitch.org/
- pfsense, opnsense, vyos
- Duplicati
- IPAM: https://netbox.readthedocs.io/en/stable/
- https://github.com/awesome-selfhosted/awesome-selfhosted

### VMs

### pivpn

OpenVPN

### k3s

```
## Installation

curl -sfL https://get.k3s.io | sh -

### Dashboard

Deploy dashboard:
GITHUB_URL=https://github.com/kubernetes/dashboard/releases
VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
sudo k3s kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml

# cat k8s-dashboard.yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard

kubectl create -f k8s-dashboard.yml

# cat /usr/bin/get-token
sudo k3s kubectl -n kubernetes-dashboard describe secret admin-user-token | grep '^token'

chmod +x /usr/bin/get-token

## Setup kubectl

# Get/fix kubectl on local system
scp root@192.168.1.50:/etc/rancher/k3s/k3s.yaml ~/.kube/config
# fix IP or domain in ~/.kube/config

## Portainer

kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
kubectl apply -n portainer -f https://raw.githubusercontent.com/portainer/k8s/master/deploy/manifests/portainer/portainer-lb.yaml

Put in /etc/profile
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml

## Enable traefik dashboard

Apply traefik dashboard access on: http://kubepi.x32.in/dashboard/

cp /var/lib/rancher/k3s/server/manifests/traefik.yaml  traefik.yaml
add the following and apply the yaml:
    dashboard:
        enabled: true
        domain: "kubepi.x32.in"
```

Gitops experiment:
- https://github.com/yadavcloud/hello-k3s/actions
- https://hub.docker.com/r/bhaisaab/hello-k3s
- Public app: https://yadav.cloud/app
- Private app: http://hello.x32.in

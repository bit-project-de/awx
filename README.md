### Ansible AWX install microk8s

Prerequisites:
 - kubectl & valid KUBE_CONFIG
 - helm 2/3

Enable local storage provisioner (microk8s only)

```bash
microk8s enable hostpath-storage
```

Add AWX-Operator Helm repo:

```bash
helm repo add awx-operator https://ansible-community.github.io/awx-operator-helm/
```

Install AWX-Operator chart in awx-operator namespace:

```bash
helm install awx-operator awx-operator/awx-operator -n awx-operator --create-namespace
```

Define AWX instance in awx-instance.yml:

```yaml
---
apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx-instance
spec:
  service_type: nodeport
```

Apply yaml to create AWX instance:

```bash
kubectl apply -n awx-operator -f workspace/project/awx/awx-instance.yml
```

Retrieve initial admin password after deployment finished:

```bash
kubectl get secret awx-instance-admin-password -o jsonpath="{.data.password}" | base64 --decode ; echo
```

Get nodeport:

```bash
kubectl get svc | grep NodePort
```

Access AWX webui at http://IP_OF_YOUR_K8S:NodePort and login as ```admin``` using the retrieved password.
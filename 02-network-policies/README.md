# 02 - Network Policies

## 🎯 Objective
Implement NetworkPolicies to enforce:
- **Default Deny** (all ingress + egress blocked)
- **Allow DNS** (egress to kube-system on port 53)
- **Allow Ingress** (frontend → nginx on port 80)
- **Allow Egress** (egress to pods on 80/443 + DNS)

## 📦 Resources

| Resource | Name | Purpose |
|----------|------|---------|
| NetworkPolicy | `default-deny-all` | Block all ingress + egress |
| NetworkPolicy | `allow-dns` | Allow DNS egress |
| NetworkPolicy | `allow-nginx` | Allow frontend → nginx (ingress) |
| NetworkPolicy | `allow-egress` | Allow egress to pods (80/443) + DNS |

## 🚀 Apply (Declarative)

    kubectl apply -f 02-network-policies/

## 🧪 Test

    # 1. Create test pods
    kubectl run nginx --image=nginx:1.25-alpine -n secure-app --labels="app=nginx" --port=80
    kubectl run frontend --image=alpine:3.19 -n secure-app --labels="app=frontend" -- sleep 3600
    kubectl run attacker --image=alpine:3.19 -n secure-app --labels="app=attacker" -- sleep 3600

    # 2. Expose nginx
    kubectl expose pod nginx -n secure-app --name=nginx --port=80 --target-port=80

    # 3. Test DNS
    kubectl exec -it frontend -n secure-app -- nslookup nginx.secure-app.svc.cluster.local

    # 4. Test frontend → nginx (should succeed)
    kubectl exec -it frontend -n secure-app -- wget -qO- --timeout=3 http://nginx:80

    # 5. Test attacker → nginx (should timeout)
    kubectl exec -it attacker -n secure-app -- wget -qO- --timeout=3 http://nginx:80

## 📸 Result

    # frontend → nginx
    <!DOCTYPE html>
    <html>
    <head><title>Welcome to nginx!</title></head>
    ...
    </html>

    # attacker → nginx
    wget: download timed out

## 🧠 What I Learned
- Default Deny principle
- Allow DNS egress
- Allow Ingress (pod-to-pod)
- Allow Egress (to pods + DNS)
- How to test NetworkPolicies

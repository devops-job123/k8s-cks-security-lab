# 01 - RBAC (Least Privilege)

## 🎯 Objective
Implement Role-Based Access Control (RBAC) with least-privilege principles.

## 📦 Resources

| Resource | Name | Purpose |
|----------|------|---------|
| Namespace | `secure-app` | Isolated namespace |
| ServiceAccount | `app-sa` | Application identity |
| Role | `app-reader` | Read-only access to pods/configmaps |
| RoleBinding | `app-reader-binding` | Binds Role to ServiceAccount |

## 🚀 Apply (Imperative)

    kubectl create namespace secure-app
    kubectl create serviceaccount app-sa -n secure-app
    kubectl create role app-reader --verb=get,list --resource=pods,configmaps -n secure-app
    kubectl create rolebinding app-reader-binding --role=app-reader --serviceaccount=secure-app:app-sa -n secure-app

## 🚀 Apply (Declarative)

    kubectl apply -f 01-rbac/

## ✅ Verify

    kubectl auth can-i list pods --as=system:serviceaccount:secure-app:app-sa -n secure-app
    kubectl auth can-i delete pods --as=system:serviceaccount:secure-app:app-sa -n secure-app
    kubectl auth can-i get secrets --as=system:serviceaccount:secure-app:app-sa -n secure-app

## 📸 Result

    yes
    no
    no

## 🧠 What I Learned
- How to create a ServiceAccount
- How to define a Role with specific verbs
- How to bind a Role to a ServiceAccount
- How to verify permissions with kubectl auth can-i
- Least Privilege principle in practice

# Open-WebUI Kubernetes Deployment

A secure Kubernetes deployment configuration for Open-WebUI with Redis backend, designed for production use with Traefik ingress and persistent storage.

## 🚀 Features

- **Secure Configuration**: No hardcoded secrets, template-based deployment
- **Redis Backend**: StatefulSet with persistent storage
- **Traefik Integration**: HTTPS ingress with automatic redirect
- **Persistent Storage**: Longhorn-backed PVC for data persistence
- **Production Ready**: Resource limits, health checks, and proper namespacing

## 📁 Project Structure

```
├── open-webui-deployment.yaml     # Main StatefulSet and Redis deployment
├── open-webui-service.yaml        # Service definitions
├── open-webui-ingress.yaml        # Traefik ingress configuration
├── open-webui-pvc.yaml            # Persistent volume claims
├── open-webui-namespace.yaml      # Namespace definition
├── open-webui-middleware.yaml     # HTTPS redirect middleware
├── open-webui-secret.yaml         # Secret with placeholder (DO NOT USE)
├── open-webui-secret-template.yaml # Secure template for secrets
├── backup/                        # Backup configurations
└── .gitignore                     # Protects sensitive files
```

## 🔐 Security

**⚠️ IMPORTANT**: This repository uses secure templates to prevent credential leaks.

### Secret Management

1. **Never use `open-webui-secret.yaml`** - contains placeholder only
2. **Use the template**: `open-webui-secret-template.yaml`
3. **Deploy securely**:
   ```bash
   # Method 1: Environment variables
   export ADMIN_PASSWORD_B64=$(echo -n 'your-secure-password' | base64)
   envsubst < open-webui-secret-template.yaml | kubectl apply -f -
   
   # Method 2: Direct kubectl
   kubectl create secret generic open-webui-secret \
     --namespace=open-webui \
     --from-literal=ADMIN_PASSWORD='your-secure-password'
   ```

## 🚀 Quick Deploy

```bash
# 1. Create namespace
kubectl apply -f open-webui-namespace.yaml

# 2. Create secret (replace with your password)
kubectl create secret generic open-webui-secret \
  --namespace=open-webui \
  --from-literal=ADMIN_PASSWORD='your-secure-admin-password'

# 3. Deploy all components
kubectl apply -f open-webui-pvc.yaml
kubectl apply -f open-webui-deployment.yaml
kubectl apply -f open-webui-service.yaml
kubectl apply -f open-webui-middleware.yaml
kubectl apply -f open-webui-ingress.yaml
```

## 🔧 Configuration

### Domain Configuration
Update `open-webui-ingress.yaml` with your domain:
```yaml
spec:
  tls:
  - hosts:
    - YOUR_OPENWEBUI_DOMAIN_HERE
    secretName: YOUR_TLS_SECRET_NAME
  rules:
  - host: YOUR_OPENWEBUI_DOMAIN_HERE
```

Replace placeholders before deployment:
```bash
sed -i 's/YOUR_OPENWEBUI_DOMAIN_HERE/openwebui.yourdomain.com/g' open-webui-ingress.yaml
sed -i 's/YOUR_TLS_SECRET_NAME/your-tls-secret/g' open-webui-ingress.yaml
```

### Storage Configuration
The deployment uses Longhorn for persistent storage. Modify `open-webui-pvc.yaml` if using a different storage class.

## 📊 Monitoring

Check deployment status:
```bash
kubectl get pods -n open-webui
kubectl get services -n open-webui
kubectl get ingress -n open-webui
```

## 🛡️ Security Best Practices

- ✅ No hardcoded secrets in manifests
- ✅ Secure template-based deployment
- ✅ GitIgnore protects sensitive files
- ✅ Resource limits configured
- ✅ HTTPS-only access via Traefik
- ✅ Namespace isolation

## 📝 License

This configuration is provided as-is for educational and production use.

# configuration-eos

```
up login
```

```
export VERSION=v0.0.1
crossplane xpkg build --package-file configuration-eos-${VERSION}.xpkg
up xpkg push -f configuration-eos-v0.0.1.xpkg xpkg.upbound.io/netclab/configuration-eos:${VERSION}
rm configuration-eos-${VERSION}.xpkg
```

---

```
helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update

helm install crossplane crossplane-stable/crossplane \
  --namespace crossplane-system --create-namespace
```

---

```
cat <<EOF | kubectl apply -f -
apiVersion: pkg.crossplane.io/v1
kind: Configuration
metadata:
  name: netclab-configuration-eos
spec:
  package: xpkg.upbound.io/netclab/configuration-eos:v0.0.1
EOF
```

---

```
cat <<EOF | kubectl apply -f -
apiVersion: http.crossplane.io/v1alpha1
kind: ProviderConfig
metadata:
  name: default
spec:
  credentials:
    source: None
---
apiVersion: apiextensions.crossplane.io/v1beta1
kind: EnvironmentConfig
metadata:
  name: endpoint-protocols
data:
  restconf:
    scheme: https
    port: 6020
  jsonrpc:
    scheme: http
    port: 6021
---
apiVersion: v1
kind: Secret
metadata:
  name: eos-creds
  namespace: crossplane-system
type: Opaque
data:
  basicAuth: YXJpc3RhOmFyaXN0YQ==
EOF
```

---

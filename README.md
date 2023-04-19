# KubeCon 2023 EU Demo

Live Example: Policy Reporter UI ([http://88.99.188.190:30582](http://88.99.188.190:30582))

![Policy Reporter UI - Demo IP](https://raw.githubusercontent.com/fjogeleit/kubecon2023-demo/main/qrcode.png)


## Monitoring

### Repositories

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

### Prometheus + Grafana

```bash
helm upgrade --install monitoring prometheus-community/kube-prometheus-stack -n monitoring --create-namespace -f values/monitoring.yaml
```

### Loki

```bash
helm upgrade --install loki grafana/loki-stack -n monitoring --create-namespace
```

## Kyverno + PolicyReport CRDs

### Repositories

```bash
helm repo add kyverno https://kyverno.github.io/kyverno/
```

### Kyverno + Pod Security Standard Policies

```bash
helm upgrade --install kyverno kyverno/kyverno -n kyverno --create-namespace
helm upgrade --install kyverno-policies kyverno/kyverno-policies -n kyverno --create-namespace --set podSecurityStandard=restricted
```

## Trivy Operator + Trivy Operator Policy Adapter

### Repositories

```bash
helm repo add trivy-operator-polr-adapter https://fjogeleit.github.io/trivy-operator-polr-adapter
helm repo add aqua https://aquasecurity.github.io/helm-charts/
```

### Trivy Operator

```bash
helm upgrade --install trivy-operator aqua/trivy-operator -n trivy-system --create-namespace --set="trivy.ignoreUnfixed=true" --set="excludeNamespaces=kube-system"
```

### Trivy Operator PolicyReport Adapter

```bash
helm upgrade --install trivy-operator-polr-adapter trivy-operator-polr-adapter/trivy-operator-polr-adapter -n trivy-system --create-namespace -f values/trivy-polr-adapter.yaml
```

## Policy Reporter

### Repository

```bash
helm repo add policy-reporter https://kyverno.github.io/policy-reporter
```

## Policy Reporter

```bash
helm upgrade --install policy-reporter policy-reporter/policy-reporter --create-namespace -n policy-reporter -f values/policy-reporter.yaml
```

## Forwardings

### Policy Reporter UI

```bash
kubectl port-forward service/policy-reporter-ui 8082:8080 -n policy-reporter
```

Open: [http://localhost:8082](http://localhost:8082)

### Grafana

Login:

Username: `admin`
Password: `prom-operator`

```bash
kubectl port-forward service/monitoring-grafana 3000:80 -n monitoring
```

Open: [http://localhost:3000](http://localhost:3000)
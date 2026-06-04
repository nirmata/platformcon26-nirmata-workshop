# Resource Optimization Demo with Kyverno

This demo showcases how to use Kyverno policies to optimize resource allocations in Kubernetes clusters by leveraging the Vertical Pod Autoscaler (VPA) recommender.

## Overview

The demo demonstrates:
1. Automatic VPA generation for Deployments and StatefulSets
2. Resource optimization validation based on VPA recommendations
3. Policy reporting for resource violations
4. Integration with Kubernetes Metrics Server

## Prerequisites

- Kind cluster
- kubectl
- helm
- Access to internet for pulling images

## Setup Steps

### 1. Create Kind Cluster

```bash
kind create cluster --name resource-optimizer
```

### 2. Install Metrics Server

```bash
# Install metrics server
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# Patch metrics server to work with kind
kubectl patch deployment metrics-server -n kube-system \
  --type='json' \
  -p='[{"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}]'

# Verify metrics server is working
kubectl top nodes
kubectl top pods -A
```

### 3. Install Kyverno

```bash
# Add Kyverno helm repo
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update

# Install Kyverno
helm install kyverno kyverno/kyverno -n kyverno --create-namespace
```

### 4. Install VPA Recommender

```bash
# Install VPA recommender
kubectl apply -f https://raw.githubusercontent.com/nirmata/demo-resource-optimizer/main/config/vpa/install-vpa-recommender.yaml
```

### 5. Configure Kyverno RBAC

```bash
# Apply RBAC configuration
kubectl apply -f https://raw.githubusercontent.com/nirmata/demo-resource-optimizer/main/config/kyverno/rbac.yaml
```

### 6. Install Kyverno Policies

```bash
# Install VPA generation policy
kubectl apply -f https://raw.githubusercontent.com/nirmata/demo-resource-optimizer/main/config/kyverno/policies/generate-vpa.yaml

# Install resource check policy
kubectl apply -f https://raw.githubusercontent.com/nirmata/demo-resource-optimizer/main/config/kyverno/policies/check-resources.yaml
```

## Demo Workload

### 1. Deploy Sample Workload

```bash
kubectl apply -f https://raw.githubusercontent.com/nirmata/demo-resource-optimizer/main/config/workload/demo-kyverno-vpa.yaml
```

### 2. Monitor VPA Recommendations

```bash
# Watch VPA recommendations
kubectl -n demo-kyverno-vpa get vpa -w
```

### 3. Check Policy Reports

```bash
# Watch policy reports
kubectl -n demo-kyverno-vpa get polr -w

# Trigger policy evaluation
kubectl -n demo-kyverno-vpa label deployments demo update=true
```

### 4. View Detailed Policy Reports

```bash
# Get detailed policy report
kubectl -n demo-kyverno-vpa get polr -o yaml
```

## Demo Scenarios

### Scenario 1: Resource Underprovisioning
1. Deploy workload with minimal resources
2. Observe VPA recommendations
3. Check policy reports for violations
4. Adjust resources based on recommendations

### Scenario 2: Resource Overprovisioning
1. Deploy workload with excessive resources
2. Observe VPA recommendations
3. Check policy reports for violations
4. Optimize resources based on recommendations

### Scenario 3: Dynamic Resource Adjustment
1. Apply load to the workload
2. Watch VPA recommendations change
3. Observe policy reports update
4. Verify resource optimization

## Cleanup

```bash
# Delete the kind cluster
kind delete cluster --name resource-optimizer
```

## Key Features Demonstrated

1. **Automatic VPA Generation**
   - Kyverno automatically creates VPA resources for Deployments and StatefulSets
   - VPA recommender provides resource recommendations

2. **Resource Optimization**
   - Policies validate resource requests against VPA recommendations
   - Reports violations for under/over-provisioned resources

3. **Policy Reporting**
   - Real-time policy violation reporting
   - Detailed resource optimization recommendations

4. **Integration**
   - Works with Kubernetes Metrics Server
   - Integrates with VPA recommender
   - Uses Kyverno's native policy reporting

## Troubleshooting

1. If metrics server is not working:
   - Verify the insecure TLS patch is applied
   - Check metrics server logs

2. If VPA recommendations are not appearing:
   - Ensure metrics server is working
   - Check VPA recommender logs

3. If policy reports are not updating:
   - Trigger policy evaluation with label update
   - Check Kyverno logs for errors 
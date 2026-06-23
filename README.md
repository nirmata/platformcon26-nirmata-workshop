# PaC-workshop with Kyverno

## Pre-Requisites
To get the most out of this workshop, you'll need the following:

* **kubectl** [Install kubectl](https://kubernetes.io/docs/tasks/tools/)
* **kustomize** [Install kustomize](https://kubectl.docs.kubernetes.io/installation/kustomize/)
* Access to a Kubernetes cluster.  You can use a local single-node cluster environment such as:
   * **kind:** [Installation Guide](https://kind.sigs.k8s.io/docs/user/quick-start/)
   * **minikube:** [Installation Guide/](https://minikube.sigs.k8s.io/docs/start/)
   * **k3d:** [Installation Guide](https://k3d.io/v5.4.6/usage/)
* Docker Desktop [Installation Guide](https://docs.docker.com/desktop/)
* **helm:** To install Kyverno OSS [Install helm](https://helm.sh/docs/intro/install/)
* An IDE/ vim to customize the policies
* [Nirmata CLI](https://docs.nirmata.io/docs/nctl/installation/)
* Nirmata Account created [here](try.nirmata.io)

## Kyverno Documentation & Resources

* **Kyverno Official Documentation:** [https://kyverno.io/docs/](https://kyverno.io/docs/) -  The official source for all things Kyverno.
* **Kyverno Policy Library:**
   * **Kyverno website:** [https://kyverno.io/policies/](https://kyverno.io/policies/) -  A collection of ready-to-use Kyverno policies.
   * **Github repo:** [https://github.com/kyverno/policies/](https://github.com/kyverno/policies/) - The source code for the policy library, allowing you to contribute or policies.


## Setting up the Workshop Environment

* **Onboarding Cluster in Nirmata**
   * **Step 1:** Add the Nirmata helm chart to the list
   ```
   helm repo add nirmata https://nirmata.github.io/kyverno-charts/
   helm repo update nirmata
   ```
   * **Step 2:** Install Nirmata Kube Controller
   ```
  helm install nirmata-kube-controller nirmata/nirmata-kube-controller \
  --version 0.3.15 -n nirmata --create-namespace \
  --set cluster.name=rfeds \
  --set apiToken=Nv27jxE9vHyhHpyiygWYKUEhjCQQS/j3LWeDeju2ciBrFHqJnSv5zFHSK+FVC+9VL8I/b4nsZaUPo8WWnbAYcw== \
  --set features.policyExceptions.enabled=true \
  --set features.policySets.enabled=true \
  --set clusterOnboardingToken=MGZiNjk2YzYtNzNlOC00YzQzLWI3MDItNmE5MGVlMTAxMWQ0
   ```
   * **Step 3:** Install Enterprise Kyverno
   ```
  helm install kyverno nirmata/kyverno \
  --version 3.7.1 -n kyverno --create-namespace \
  --set features.policyExceptions.namespace="kyverno" \
  --set crds.reportsServer.enabled=false \
  --set features.policyExceptions.enabled=true
   ```

    * **Step 4:** Install the Nirmata Kyverno Operator
   ```
  helm install kyverno-operator nirmata/nirmata-kyverno-operator \
  --version 0.9.1 -n nirmata-system \
  --create-namespace \
  --set enablePolicyset=true
   ```
   
 * **Step 5:** Deploy pod secuPolicies 

```
    kustomize build https://github.com/nirmata/kyverno-policies/pod-security/audit | kubectl apply -f - 
  ```
   



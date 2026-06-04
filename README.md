# PaC-workshop with Kyverno

## Pre-Requisites
To get the most out of this workshop, you'll need the following:

* **kubectl** [Install kubectl](https://kubernetes.io/docs/tasks/tools/)
* Access to a Kubernetes cluster.  You can use a local single-node cluster environment such as:
   * **kind:** [Installation Guide](https://kind.sigs.k8s.io/docs/user/quick-start/)
   * **minikube:** [Installation Guide/](https://minikube.sigs.k8s.io/docs/start/)
   * **k3d:** [Installation Guide](https://k3d.io/v5.4.6/usage/)
* Docker Desktop [Installation Guide](https://docs.docker.com/desktop/)
* **helm:** To install Kyverno OSS [Install helm](https://helm.sh/docs/intro/install/)
* An IDE/ vim to customize the policies

## Kyverno Documentation & Resources

* **Kyverno Official Documentation:** [https://kyverno.io/docs/](https://kyverno.io/docs/) -  The official source for all things Kyverno.
* **Kyverno Policy Library:**
   * **Kyverno website:** [https://kyverno.io/policies/](https://kyverno.io/policies/) -  A collection of ready-to-use Kyverno policies.
   * **Github repo:** [https://github.com/kyverno/policies/](https://github.com/kyverno/policies/) - The source code for the policy library, allowing you to contribute or policies.

## Tools for Testing

* **Kyverno CLI:** [Guide](https://kyverno.io/docs/kyverno-cli/) - A tool for performing unit tests on Kyverno policies.
* **Chainsaw:** [Guide](https://kyverno.github.io/chainsaw/0.2.3/quick-start/) - A framework for E2E testing of policies.

## Setting up the Workshop Environment

* **Installing Kyverno OSS**
   * **Step 1:** Add the Kyverno helm chart to the list
   ```
   helm repo add kyverno https://kyverno.github.io/kyverno && helm repo update
   ```
   * **Step 2:** Get the Kyverno OSS chart list
   ```
   helm search repo kyverno -l
   ```
   * **Step 3:** Install Kyverno OSS (Installs the latest Kyverno OSS)
   ```
   helm install kyverno --namespace kyverno --create-namespace kyverno/kyverno
   ```
* **Install Kyverno CLI:** [Install Guide](https://kyverno.io/docs/kyverno-cli/install/)
* **Install Chainsaw** [Install Guide](https://kyverno.github.io/chainsaw/latest/quick-start/install/)

## Workshop Agenda


This workshop is structured to guide you from the fundamentals of Kyverno to more advanced use cases.

**1. Introduction to Kyverno**

* Overview of Policy-as-Code.
* Kyverno's architecture and core concepts.

**2. Writing Validation Policies**

   We'll focus on writing, deploying, and testing validation policies to enforce constraints on Kubernetes resources.

   * **Governance:** [Require Labels](https://github.com/nirmata/nirmata-kyverno-workshop/tree/main/Labs/require-labels) - Ensure that specific labels are applied to resources for organizational and management purposes.
   * **Pod Security:** [Disallow Privileged Containers](https://github.com/nirmata/nirmata-kyverno-workshop/tree/main/Labs/disallow-privileged-containers) - Implement policies to enforce security best practices for Pods.

**3. Advanced Kyverno Policy Use Cases**

   Explore Kyverno's powerful capabilities beyond basic validation.

   * **[Mutating Policy](https://github.com/nirmata/nirmata-kyverno-workshop/tree/main/Demos/01-mutation-policies):** Automatically modify resources, such as adding defaults or annotations.
   * **[Generating Policy](https://github.com/nirmata/nirmata-kyverno-workshop/tree/main/Demos/02-generation-policies):** Create new Kubernetes resources based on existing ones, automating tasks like creating ConfigMaps or Secrets.
   * **[Image Verification Policy](https://github.com/nirmata/nirmata-kyverno-workshop/tree/main/Demos/03-image-verification):** Implement policies to ensure that only trusted container images are deployed in your cluster, enhancing security.
   * **[Resource Optimization](https://github.com/nirmata/nirmata-kyverno-workshop/tree/main/Demos/04-resource-optimization):** Explore policies that can help optimize resource utilization.

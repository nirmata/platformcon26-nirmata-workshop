# Image Verification Policy

The image verification policy enforces image signature verification for container images. The policy ensures that only signed images can be deployed in the Kubernetes cluster.

## Policy Overview

The `check-image.yaml` policy enforces the following rules:

- Applies to all Pod resources
- Verifies images matching the pattern `index.docker.io/sagarkundral/nginx**`
- Uses Sigstore's Rekor transparency log for verification
- Enforces signature verification (blocks unsigned images)
- Uses a specific public key for verification

## Prerequisites

- Kubernetes cluster with Kyverno installed
- Cosign CLI tool
- Docker CLI
- Access to push images to a container registry

## Usage

### 1. Generate Cosign Key Pair

```bash
cosign generate-key-pair
```

This will create:
- `cosign.key` (private key)
- `cosign.pub` (public key)

### 2. Sign and Push Images

To sign and push an image:

```bash
# Tag your image
docker tag nginx:latest index.docker.io/sagarkundral/nginx:signed-image

# Push the image
docker push index.docker.io/sagarkundral/nginx:signed-image

# Sign the image with your private key
cosign sign --key cosign.key index.docker.io/sagarkundral/nginx:signed-image
```

### 3. Apply the Policy

```bash
kubectl apply -f check-image.yaml
```

### 4. Test the Policy

Try running an unsigned image:
```bash
kubectl run unsigned --image=index.docker.io/sagarkundral/nginx:unsigned-image
```
This will be blocked by the policy with below error

```bash
kubectl run unsigned --image=index.docker.io/sagarkundral/nginx:unsigned-image
Error from server: admission webhook "mutate.kyverno.svc-fail" denied the request:

resource Pod/default/unsigned was blocked due to the following policies

check-image:
  check-image: 'failed to verify image index.docker.io/sagarkundral/nginx:unsigned-image:
    .attestors[0].entries[0].keys: no signatures found'
```

Try running a signed image:
```bash
kubectl run nginx-signed --image=index.docker.io/sagarkundral/nginx:signed-image
```
This should be allowed.

## Policy Details

The policy:
- Uses Sigstore's Rekor transparency log for verification
- Ignores the transparency log entries (`ignoreTlog: true`)
- Requires at least one valid signature (`count: 1`)
- Uses a specific public key for verification
- Enforces the policy (blocks unsigned images)

## Troubleshooting

If you encounter issues:
1. Verify that the image is properly signed
2. Check that the public key in the policy matches your signing key
3. Ensure the image reference pattern matches your images
4. Check Kyverno logs for detailed error messages

## Cleanup

To remove the policy:
```bash
kubectl delete cpol check-image
``` 

# Namespace Cloud Build Example

This repository shows how to use [Namespace Cloud](https://cloud.namespace.so/) to build a sample Docker container.

## Run It Yourself!

1. [Fork](https://github.com/namespacelabs/examples-nsc-build-simple/fork) this repository;
2. Enable the GitHub Actions for it;
3. Configure the GitHub Actions to have `write` permissions on the repository;
   1. Go to _Settings_, then _Actions_ and finally _General_;
   2. Scroll down to _Workflow permissions_;
   3. Set _Read and write permissions_ option;
4. Manually run the _build_ workflow;
5. Pull the image locally!

   `docker pull ghcr.io/< your username >/nsc-django-example:v0.0.1`

## Examples

### Namespace Cloud CLI

This example uses `nsc` CLI to build and push a Docker image to GitHub Container Registry.

```yaml
name: build-with-cli
on: [push]

permissions:
  # Required for requesting the GitHub Token
  id-token: write
  # Required for pushing images to GitHub Container Registry
  packages: write

jobs:
  build_with_nscloud:
    runs-on: ubuntu-latest
    name: Build with Namespace Cloud
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      # We are going to push to GH Container Registry
      - name: Log in to GitHub registry
        run: echo "${{ secrets.GITHUB_TOKEN }}" | docker login ghcr.io -u $ --password-stdin

      # Install CLI and authenticate to Namespace Cloud
      - name: Install and configure Namespace Cloud CLI
        uses: namespacelabs/nscloud-setup@v0.0.2

      # Build and push with your tenant's Namespace Cloud cluster
      - name: Build and push with Namespace Cloud cluster
        run: |
          nsc build . --push ghcr.io/${{ github.repository_owner }}/app --tag latest
```

### Github Actions

This example uses Namespace Cloud Actions to build and push a Docker image to GitHub Container Registry.

```yaml
name: build-with-actions
on: [push]

permissions:
  # Required for requesting the GitHub Token
  id-token: write
  # Required for pushing images to GitHub Container Registry
  packages: write

jobs:
  build_with_nscloud:
    runs-on: ubuntu-latest
    name: Build with Namespace Cloud
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      # We are going to push to GH Container Registry
      - name: Login to GitHub Container Registry
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      # Install CLI and authenticate to Namespace Cloud
      - name: Install and configure Namespace Cloud CLI
        uses: namespacelabs/nscloud-setup@v0.0.2

      # Setup build driver to use Buildkit hosted by your tenant's Namespace Cloud cluster
      - name: Set up Nanespace Cloud Buildx
        uses: namespacelabs/nscloud-setup-buildx-action@v0.0.2

      # Run standard Docker's build-push action
      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: ghcr.io/${{ github.repository_owner }}/app:latest
```

## Community

Join us on [Discord](https://community.namespace.so/discord) for questions or feedbacks!

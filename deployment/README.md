# Coworking Analytics Service

## Architecture

GitHub
  → AWS CodeBuild
  → Amazon ECR
  → Amazon EKS
  → Coworking Analytics Service
  → PostgreSQL

## CI Process

CodeBuild is triggered from the GitHub repository.
It builds the Docker image from analytics/Dockerfile and
publishes it to Amazon ECR using semantic image tags.

## Deployment Process

Kubernetes manifests define the application's desired state.
The coworking deployment retrieves its image from ECR.

Application configuration is provided using:
- ConfigMap for DB_HOST, DB_PORT, DB_NAME and DB_USERNAME
- Secret for DB_PASSWORD

The analytics service communicates with PostgreSQL internally
using postgresql-service:5432.

## Releasing a New Version

1. Commit and push application changes.
2. CodeBuild creates and publishes a new ECR image.
3. Update the image version in deployment/coworking.yaml.
4. Apply the Kubernetes manifest.
5. Verify rollout health and application endpoints.

## Monitoring

Amazon CloudWatch Container Insights collects logs and
observability data from the EKS cluster.

## Resource Allocation

The analytics service defines CPU and memory requests and limits
to prevent individual workloads from consuming excessive node
resources.

## Recommended Instance Type

For the current low-traffic educational workload, a burstable
general-purpose instance such as t3.small/t3.medium is sufficient.
Production sizing should instead be based on observed CPU,
memory, request volume and availability requirements.

## Cost Optimization

Costs can be reduced by right-sizing worker nodes, configuring
autoscaling, minimizing unnecessary LoadBalancers, and using
appropriate log-retention policies in CloudWatch.
## Essential Components in Kubernetes

- **Secrets:** Securely store sensitive data like passwords and tokens.
- **Init Containers:** Run initialization tasks before main containers start.
- **Static Containers:** Managed directly by kubelet, used for critical node-level services.
- **Probes:** Health checks (liveness, readiness, startup) to monitor container health.
- **Resource Requests and Limits:** Specify CPU/memory minimums and maximums for containers.
- **Namespaces:** Logical partitions to isolate and organize cluster resources.

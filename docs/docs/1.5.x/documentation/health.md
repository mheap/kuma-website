# Dataplane Health

Health is an important aspect of a microservice architecture. Kuma can use health status
to select endpoints for communication between dataplane proxies.
Orchestrators, such as Kubernetes, use service health status to manage container lifecycles.
Also, users want the service state to be observable through the GUI or CLI.


Kuma supports several health checking methods:

## [Circuit Breaker](../../policies/circuit-breaker) Policy

  A **passive** Kuma policy which configures a dataplane proxy to monitor its existing
  mesh traffic in order to evaluate dataplane health. The dataplane can be configured to
  respond to a widely configurable range of errors and events that it may detect in communication
  with remote endpoints.

## [Kubernetes](../../policies/service-health-probes#kubernetes-probes) and [Universal](../../policies/service-health-probes#universal-probes) Service Probes

  Configuration of centralized health probing of services, either directly by Kuma Control Plane,
  or by the underlying platform, such as Kubernetes.  These can detect problems from the
  Control Plane's perspective, and propagate failures to the entire mesh. However, it is necessary
  for the Control Plane to be available, unlike policies which operate independently on the
  dataplane itself.

## [Health Check](../../policies/health-check) Policy

  An **active** Kuma policy which configures a dataplane proxy to send extra traffic
  to other dataplane proxies in order to evaluate their health. The amount of extra traffic
  for all dataplane proxies to actively probe each other grows quickly for large meshes. In some
  meshes, Health Check can be useful for specific routes which are not frequently traversed,
  but still need to detect failures quickly.

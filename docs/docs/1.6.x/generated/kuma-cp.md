# Control-Plane configuration
Here are all options to configure the control-plane:

```yaml
# Environment Type, can be either "kubernetes" or "universal"
environment: universal # ENV: KUMA_ENVIRONMENT
mode: standalone # ENV: KUMA_MODE By default autoconfigured to standalone

# Resource Store configuration
store:
  # Type of Store used in the Control Plane. Can be either "kubernetes", "postgres" or "memory"
  type: memory # ENV: KUMA_STORE_TYPE

  # Kubernetes Store configuration (used when store.type=kubernetes)
  kubernetes:
    # Namespace where Control Plane is installed to.
    systemNamespace: kuma-system # ENV: KUMA_STORE_KUBERNETES_SYSTEM_NAMESPACE

  # Postgres Store configuration (used when store.type=postgres)
  postgres:
    # Host of the Postgres DB
    host: 127.0.0.1 # ENV: KUMA_STORE_POSTGRES_HOST
    # Port of the Postgres DB
    port: 15432 # ENV: KUMA_STORE_POSTGRES_PORT
    # User of the Postgres DB
    user: kuma # ENV: KUMA_STORE_POSTGRES_USER
    # Password of the Postgres DB
    password: kuma # ENV: KUMA_STORE_POSTGRES_PASSWORD
    # Database name of the Postgres DB
    dbName: kuma # ENV: KUMA_STORE_POSTGRES_DB_NAME
    # Connection Timeout to the DB in seconds
    connectionTimeout: 5 # ENV: KUMA_STORE_POSTGRES_CONNECTION_TIMEOUT
    # Maximum number of open connections to the database
    # `0` value means number of open connections is unlimited
    maxOpenConnections: 50 # ENV: KUMA_STORE_POSTGRES_MAX_OPEN_CONNECTIONS
    # Maximum number of connections in the idle connection pool
    # <0 value means no idle connections and 0 means default max idle connections
    maxIdleConnections: 50  # ENV: KUMA_STORE_POSTGRES_MAX_IDLE_CONNECTIONS
    # TLS settings
    tls:
      # Mode of TLS connection. Available values (disable, verifyNone, verifyCa, verifyFull)
      mode: disable # ENV: KUMA_STORE_POSTGRES_TLS_MODE
      # Path to TLS Certificate of the client. Used in verifyCa and verifyFull modes
      certPath: # ENV: KUMA_STORE_POSTGRES_TLS_CERT_PATH
      # Path to TLS Key of the client. Used in verifyCa and verifyFull modes
      keyPath: # ENV: KUMA_STORE_POSTGRES_TLS_KEY_PATH
      # Path to the root certificate. Used in verifyCa and verifyFull modes.
      caPath: # ENV: KUMA_STORE_POSTGRES_TLS_ROOT_CERT_PATH
    # MinReconnectInterval controls the duration to wait before trying to
    # re-establish the database connection after connection loss. After each
    # consecutive failure this interval is doubled, until MaxReconnectInterval
    # is reached. Successfully completing the connection establishment procedure
    # resets the interval back to MinReconnectInterval.
    minReconnectInterval: "10s" # ENV: KUMA_STORE_POSTGRES_MIN_RECONNECT_INTERVAL
    # MaxReconnectInterval controls the maximum possible duration to wait before trying
    # to re-establish the database connection after connection loss.
    maxReconnectInterval: "60s" # ENV: KUMA_STORE_POSTGRES_MAX_RECONNECT_INTERVAL

  # Cache for read only operations. This cache is local to the instance of the control plane.
  cache:
    # If true then cache is enabled
    enabled: true # ENV: KUMA_STORE_CACHE_ENABLED
    # Expiration time for elements in cache.
    expirationTime: 1s # ENV: KUMA_STORE_CACHE_EXPIRATION_TIME

  # Upsert configuration
  upsert:
    # Base time for exponential backoff on upsert (get and update) operations when retry is enabled
    conflictRetryBaseBackoff: 100ms # ENV: KUMA_STORE_UPSERT_CONFLICT_RETRY_BASE_BACKOFF
    # Max retries on upsert (get and update) operation when retry is enabled
    conflictRetryMaxTimes: 5 # ENV: KUMA_STORE_UPSERT_CONFLICT_RETRY_MAX_TIMES

# Configuration of Bootstrap Server, which provides bootstrap config to Dataplanes
bootstrapServer:
  # The version of Envoy API (available: "v3")
  apiVersion: v3
  # Parameters of bootstrap configuration
  params:
    # Address of Envoy Admin
    adminAddress: 127.0.0.1 # ENV: KUMA_BOOTSTRAP_SERVER_PARAMS_ADMIN_ADDRESS
    # Port of Envoy Admin
    adminPort: 9901 # ENV: KUMA_BOOTSTRAP_SERVER_PARAMS_ADMIN_PORT
    # Path to access log file of Envoy Admin
    adminAccessLogPath: /dev/null # ENV: KUMA_BOOTSTRAP_SERVER_PARAMS_ADMIN_ACCESS_LOG_PATH
    # Host of XDS Server. By default it is the same host as the one used by kuma-dp to connect to the control plane
    xdsHost: "" # ENV: KUMA_BOOTSTRAP_SERVER_PARAMS_XDS_HOST
    # Port of XDS Server. By default it is autoconfigured from KUMA_XDS_SERVER_GRPC_PORT
    xdsPort: 0 # ENV: KUMA_BOOTSTRAP_SERVER_PARAMS_XDS_PORT
    # Connection timeout to the XDS Server
    xdsConnectTimeout: 1s # ENV: KUMA_BOOTSTRAP_SERVER_PARAMS_XDS_CONNECT_TIMEOUT

#  Monitoring Assignment Discovery Service (MADS) server configuration
monitoringAssignmentServer:
  # Port of a gRPC server that serves Monitoring Assignment Discovery Service (MADS).
  port: 5676 # ENV: KUMA_MONITORING_ASSIGNMENT_SERVER_PORT
  # Which MADS API versions to serve
  apiVersions: # ENV: KUMA_MONITORING_ASSIGNMENT_SERVER_API_VERSIONS
    - v1
  # Interval for re-generating monitoring assignments for clients connected to the Control Plane.
  assignmentRefreshInterval: 1s # ENV: KUMA_MONITORING_ASSIGNMENT_SERVER_ASSIGNMENT_REFRESH_INTERVAL
  # The default timeout for a single fetch-based discovery request, if not specified
  defaultFetchTimeout: 30s # ENV: KUMA_MONITORING_ASSIGNMENT_SERVER_DEFAULT_FETCH_TIMEOUT

# Envoy XDS server configuration
xdsServer:
  # Interval for re-genarting configuration for Dataplanes connected to the Control Plane
  dataplaneConfigurationRefreshInterval: 1s # ENV: KUMA_XDS_SERVER_DATAPLANE_CONFIGURATION_REFRESH_INTERVAL
  # Interval for flushing status of Dataplanes connected to the Control Plane
  dataplaneStatusFlushInterval: 10s # ENV: KUMA_XDS_SERVER_DATAPLANE_STATUS_FLUSH_INTERVAL
  # Backoff that is executed when Control Plane is sending the response that was previously rejected by Dataplane
  nackBackoff: 5s # ENV: KUMA_XDS_SERVER_NACK_BACKOFF

# API Server configuration
apiServer:
  # HTTP configuration of the API Server
  http:
    # If true then API Server will be served on HTTP
    enabled: true # ENV: KUMA_API_SERVER_HTTP_ENABLED
    # Network interface on which HTTP API Server will be exposed
    interface: 0.0.0.0 # ENV: KUMA_API_SERVER_HTTP_INTERFACE
    # Port of the API Server
    port: 5681 # ENV: KUMA_API_SERVER_HTTP_PORT
  # HTTPS configuration of the API Server
  https:
    # If true then API Server will be served on HTTPS
    enabled: true # ENV: KUMA_API_SERVER_HTTPS_ENABLED
    # Network interface on which HTTPS API Server will be exposed
    interface: 0.0.0.0 # ENV: KUMA_API_SERVER_HTTPS_INTERFACE
    # Port of the HTTPS API Server
    port: 5682 # ENV: KUMA_API_SERVER_HTTPS_PORT
    # Path to TLS certificate file. Autoconfigured from KUMA_GENERAL_TLS_CERT_FILE if empty
    tlsCertFile: "" # ENV: KUMA_API_SERVER_HTTPS_TLS_CERT_FILE
    # Path to TLS key file. Autoconfigured from KUMA_GENERAL_TLS_KEY_FILE if empty
    tlsKeyFile: "" # ENV: KUMA_API_SERVER_HTTPS_TLS_KEY_FILE
  # Authentication configuration for administrative endpoints like Dataplane Token or managing Secrets
  auth:
    # Directory of authorized client certificates (only validate in HTTPS)
    clientCertsDir: "" # ENV: KUMA_API_SERVER_AUTH_CLIENT_CERTS_DIR
  # Api Server Authentication configuration
  authn:
    # Type of authentication mechanism (available values: "adminClientCerts", "tokens")
    type: tokens # ENV: KUMA_API_SERVER_AUTHN_TYPE
    # Localhost is authenticated as a user admin of group admin
    localhostIsAdmin: true # ENV: KUMA_API_SERVER_AUTHN_LOCALHOST_IS_ADMIN
    # Configuration for tokens authentication
    tokens:
      # If true then User Token with name admin and group admin will be created and placed as admin-user-token Kuma secret
      bootstrapAdminToken: true # ENV: KUMA_API_SERVER_AUTHN_TOKENS_BOOTSTRAP_ADMIN_TOKEN
  # If true, then API Server will operate in read only mode (serving GET requests)
  readOnly: false # ENV: KUMA_API_SERVER_READ_ONLY
  # Allowed domains for Cross-Origin Resource Sharing. The value can be either domain or regexp
  corsAllowedDomains:
    - ".*" # ENV: KUMA_API_SERVER_CORS_ALLOWED_DOMAINS

# Environment-specific configuration
runtime:
  # Kubernetes-specific configuration
  kubernetes:
    # Service name of the Kuma Control Plane. It is used to point Kuma DP to proper URL.
    controlPlaneServiceName: kuma-control-plane # ENV: KUMA_RUNTIME_KUBERNETES_CONTROL_PLANE_SERVICE_NAME
    # Name of Service Account that is used to run the Control Plane
    serviceAccountName: "system:serviceaccount:kuma-system:kuma-control-plane" # ENV: KUMA_RUNTIME_KUBERNETES_SERVICE_ACCOUNT_NAME
    # Admission WebHook Server configuration
    admissionServer:
      # Address the Admission WebHook Server should be listening on
      address: # ENV: KUMA_RUNTIME_KUBERNETES_ADMISSION_SERVER_ADDRESS
      # Port the Admission WebHook Server should be listening on
      port: 5443 # ENV: KUMA_RUNTIME_KUBERNETES_ADMISSION_SERVER_PORT
      # Directory with a TLS cert and private key for the Admission WebHook Server.
      # TLS certificate file must be named `tls.crt`.
      # TLS key file must be named `tls.key`.
      certDir: # ENV: kuma_runtime_kubernetes_admission_server_cert_dir
    # Injector defines configuration of a Kuma Sidecar Injector.
    injector:
      # if true runs kuma-cp in CNI compatible mode
      cniEnabled: false # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_CNI_ENABLED
      # list of exceptions for Kuma injection
      exceptions:
        # a map of labels for exception. If pod matches label with given value Kuma won't be injected. Specify '*' to match any value.
        labels:
          openshift.io/build.name: "*"
          openshift.io/deployer-pod-for.name: "*"
      # VirtualProbesEnabled enables automatic converting HttpGet probes to virtual. Virtual probe
      #	serves on sub-path of insecure port 'virtualProbesPort',
      #	i.e :8080/health/readiness -> :9000/8080/health/readiness where 9000 is virtualProbesPort
      virtualProbesEnabled: true # ENV: KUMA_RUNTIME_KUBERNETES_VIRTUAL_PROBES_ENABLED
      # VirtualProbesPort is a port for exposing virtual probes which are not secured by mTLS
      virtualProbesPort: 9000 # ENV: KUMA_RUNTIME_KUBERNETES_VIRTUAL_PROBES_PORT
      # CaCertFile is CA certificate which will be used to verify a connection to the control plane.
      caCertFile:  # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_CA_CERT_FILE
      # SidecarContainer defines configuration of the Kuma sidecar container.
      sidecarContainer:
        # Image name.
        image: kuma/kuma-dp:latest # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_IMAGE
        # Redirect port for inbound traffic.
        redirectPortInbound: 15006 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_REDIRECT_PORT_INBOUND
        # Redirect port for inbound traffic.
        redirectPortInboundV6: 15010 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_REDIRECT_PORT_INBOUND_V6
        # Redirect port for outbound traffic.
        redirectPortOutbound: 15001 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_REDIRECT_PORT_OUTBOUND
        # User ID.
        uid: 5678 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_UID
        # Group ID.
        gid: 5678 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_GUI
        # Drain time for listeners.
        drainTime: 30s # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_DRAIN_TIME
        # Readiness probe.
        readinessProbe:
          # Number of seconds after the container has started before readiness probes are initiated.
          initialDelaySeconds: 1 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_READINESS_PROBE_INITIAL_DELAY_SECONDS
          # Number of seconds after which the probe times out.
          timeoutSeconds: 3 # ENV : KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_READINESS_PROBE_TIMEOUT_SECONDS
          # Number of seconds after which the probe times out.
          periodSeconds: 5 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_READINESS_PROBE_PERIOD_SECONDS
          # Minimum consecutive successes for the probe to be considered successful after having failed.
          successThreshold: 1 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_READINESS_PROBE_SUCCESS_THRESHOLD
          # Minimum consecutive failures for the probe to be considered failed after having succeeded.
          failureThreshold: 12 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_READINESS_PROBE_FAILURE_THRESHOLD
        # Liveness probe.
        livenessProbe:
          # Number of seconds after the container has started before liveness probes are initiated.
          initialDelaySeconds: 60 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_LIVENESS_PROBE_INITIAL_DELAY_SECONDS
          # Number of seconds after which the probe times out.
          timeoutSeconds: 3 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_LIVENESS_PROBE_TIMEOUT_SECONDS
          # How often (in seconds) to perform the probe.
          periodSeconds: 5 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_LIVENESS_PROBE_PERIOD_SECONDS
          # Minimum consecutive failures for the probe to be considered failed after having succeeded.
          failureThreshold: 12 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_LIVENESS_PROBE_FAILURE_THRESHOLD
        # Compute resource requirements.
        resources:
          # Minimum amount of compute resources required.
          requests:
            # CPU, in cores. (500m = .5 cores)
            cpu: 50m # ENV: KUMA_INJECTOR_SIDECAR_CONTAINER_RESOURCES_REQUESTS_CPU
            # Memory, in bytes. (500Gi = 500GiB = 500 * 1024 * 1024 * 1024)
            memory: 64Mi # ENV: KUMA_INJECTOR_SIDECAR_CONTAINER_RESOURCES_REQUESTS_MEMORY
          # Maximum amount of compute resources allowed.
          limits:
            # CPU, in cores. (500m = .5 cores)
            cpu: 1000m # ENV: KUMA_INJECTOR_SIDECAR_CONTAINER_RESOURCES_LIMITS_CPU
            # Memory, in bytes. (500Gi = 500GiB = 500 * 1024 * 1024 * 1024)
            memory: 512Mi # ENV: KUMA_INJECTOR_SIDECAR_CONTAINER_RESOURCES_LIMITS_MEMORY
        # Additional environment variables that can be placed on Kuma DP sidecar
        envVars: # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_SIDECAR_CONTAINER_ENV_VARS
      # InitContainer defines configuration of the Kuma init container
      initContainer:
        # Image name.
        image: kuma/kuma-init:latest # ENV: KUMA_INJECTOR_INIT_CONTAINER_IMAGE
      # Configuration for a traffic that is intercepted by sidecar
      sidecarTraffic:
        # List of inbound ports that will be excluded from interception.
        # This setting is applied on every pod unless traffic.kuma.io/exclude-inbound-ports annotation is specified on Pod.
        excludeInboundPorts: [ ] # ENV: KUMA_RUNTIME_KUBERNETES_SIDECAR_TRAFFIC_EXCLUDE_INBOUND_PORTS
        # List of outbound ports that will be excluded from interception.
        # This setting is applied on every pod unless traffic.kuma.io/exclude-oubound-ports annotation is specified on Pod.
        excludeOutboundPorts: [ ] # ENV: KUMA_RUNTIME_KUBERNETES_SIDECAR_TRAFFIC_EXCLUDE_OUTBOUND_PORTS
      builtinDNS:
        # Use the built-in DNS
        enabled: true # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_BUILTIN_DNS_ENABLED
        # Redirect port for DNS
        port: 15053 # ENV: KUMA_RUNTIME_KUBERNETES_INJECTOR_BUILTIN_DNS_PORT
    marshalingCacheExpirationTime: 5m # ENV: KUMA_RUNTIME_KUBERNETES_MARSHALING_CACHE_EXPIRATION_TIME
  # Universal-specific configuration
  universal:
    # DataplaneCleanupAge defines how long Dataplane should be offline to be cleaned up by GC
    dataplaneCleanupAge: 72h0m0s # ENV: KUMA_RUNTIME_UNIVERSAL_DATAPLANE_CLEANUP_AGE

# Default Kuma entities configuration
defaults:
  skipMeshCreation: false # ENV: KUMA_DEFAULTS_SKIP_MESH_CREATION

# Metrics configuration
metrics:
  dataplane:
    # Enables collecting metrics from Dataplane
    enabled: true # ENV: KUMA_METRICS_DATAPLANE_ENABLED
    # How many latest subscriptions will be stored in DataplaneInsight object, if equals 0 then unlimited
    subscriptionLimit: 2 # ENV: KUMA_METRICS_DATAPLANE_SUBSCRIPTION_LIMIT
    # How long data plane proxy can stay Online without active xDS connection
    idleTimeout: 5m # ENV: KUMA_METRICS_DATAPLANE_IDLE_TIMEOUT
  zone:
    # Enables collecting metrics from Zone
    enabled: true # ENV: KUMA_METRICS_ZONE_ENABLED
    # How many latest subscriptions will be stored in ZoneInsights object, if equals 0 then unlimited
    subscriptionLimit: 10 # ENV: KUMA_METRICS_ZONE_SUBSCRIPTION_LIMIT
    # How long zone can stay Online without active KDS connection
    idleTimeout: 5m # ENV: KUMA_METRICS_ZONE_IDLE_TIMEOUT
  mesh:
    # Min time that should pass between MeshInsight resync
    minResyncTimeout: 1s # ENV: KUMA_METRICS_MESH_MIN_RESYNC_TIMEOUT
    # Max time that MeshInsight could spend without resync
    maxResyncTimeout: 20s # ENV: KUMA_METRICS_MESH_MAX_RESYNC_TIMEOUT

# Reports configuration
reports:
  # If true then usage stats will be reported
  enabled: false # ENV: KUMA_REPORTS_ENABLED

# General configuration
general:
  # dnsCacheTTL represents duration for how long Kuma CP will cache result of resolving dataplane's domain name
  dnsCacheTTL: 10s # ENV: KUMA_GENERAL_DNS_CACHE_TTL
  # TlsCertFile defines a path to a file with PEM-encoded TLS cert that will be used across all the Kuma Servers.
  tlsCertFile: # ENV: KUMA_GENERAL_TLS_CERT_FILE
  # TlsKeyFile defines a path to a file with PEM-encoded TLS key that will be used across all the Kuma Servers.
  tlsKeyFile: # ENV: KUMA_GENERAL_TLS_KEY_FILE
  workDir: ""

# Web GUI Server configuration
guiServer:
  # URL of the Api Server
  apiServerUrl: # ENV: KUMA_GUI_SERVER_API_SERVER_URL

# DNS Server configuration
dnsServer:
  # The domain that the server will resolve the services for
  domain: "mesh" # ENV: KUMA_DNS_SERVER_DOMAIN
  # Port on which the server is exposed
  port: 5653 # ENV: KUMA_DNS_SERVER_PORT
  # The CIDR range used to allocate
  CIDR: "240.0.0.0/4" # ENV: KUMA_DNS_SERVER_CIDR
  # Will create a service "<kuma.io/service>.mesh" dns entry for every service.
  serviceVipEnabled: true # ENV: KUMA_DNS_SERVER_SERVICE_VIP_ENABLED

# Multizone mode
multizone:
  global:
    kds:
      # Port of a gRPC server that serves Kuma Discovery Service (KDS).
      grpcPort: 5685 # ENV: KUMA_MULTIZONE_GLOBAL_KDS_GRPC_PORT
      # Interval for refreshing state of the world
      refreshInterval: 1s # ENV: KUMA_MULTIZONE_GLOBAL_KDS_REFRESH_INTERVAL
      # Interval for flushing Zone Insights (stats of multi-zone communication)
      zoneInsightFlushInterval: 10s # ENV: KUMA_MULTIZONE_GLOBAL_KDS_ZONE_INSIGHT_FLUSH_INTERVAL
      # TlsCertFile defines a path to a file with PEM-encoded TLS cert.
      tlsCertFile: # ENV: KUMA_MULTIZONE_GLOBAL_KDS_TLS_CERT_FILE
      # TTlsKeyFile defines a path to a file with PEM-encoded TLS key.
      tlsKeyFile: # ENV: KUMA_MULTIZONE_GLOBAL_KDS_TLS_KEY_FILE
      # MaxMsgSize defines a maximum size of the message in bytes that is exchanged using KDS.
      # In practice this means a limit on full list of one resource type.
      maxMsgSize: 10485760 # ENV: KUMA_MULTIZONE_GLOBAL_KDS_MAX_MSG_SIZE
  zone:
    # Kuma Zone name used to mark the zone dataplane resources
    name: "" # ENV: KUMA_MULTIZONE_ZONE_NAME
    # GlobalAddress URL of Global Kuma CP
    globalAddress: # ENV KUMA_MULTIZONE_ZONE_GLOBAL_ADDRESS
    kds:
      # Interval for refreshing state of the world
      refreshInterval: 1s # ENV: KUMA_MULTIZONE_ZONE_KDS_REFRESH_INTERVAL
      # RootCAFile defines a path to a file with PEM-encoded Root CA. Client will verify server by using it.
      rootCaFile: # ENV: KUMA_MULTIZONE_ZONE_KDS_ROOT_CA_FILE
      # MaxMsgSize defines a maximum size of the message in bytes that is exchanged using KDS.
      # In practice this means a limit on full list of one resource type.
      maxMsgSize: 10485760 # ENV: KUMA_MULTIZONE_ZONE_KDS_MAX_MSG_SIZE

# Diagnostics configuration
diagnostics:
  # Port of Diagnostic Server for checking health and readiness of the Control Plane
  serverPort: 5680 # ENV: KUMA_DIAGNOSTICS_SERVER_PORT
  # If true, enables https://golang.org/pkg/net/http/pprof/ debug endpoints
  debugEndpoints: false # ENV: KUMA_DIAGNOSTICS_DEBUG_ENDPOINTS

# Dataplane Server configuration that servers API like Bootstrap/XDS for the Dataplane.
dpServer:
  # Port of the DP Server
  port: 5678 # ENV: KUMA_DP_SERVER_PORT
  # TlsCertFile defines a path to a file with PEM-encoded TLS cert. If empty, autoconfigured from general.tlsCertFile
  tlsCertFile: # ENV: KUMA_DP_SERVER_TLS_CERT_FILE
  # TlsKeyFile defines a path to a file with PEM-encoded TLS key. If empty, autoconfigured from general.tlsKeyFile
  tlsKeyFile: # ENV: KUMA_DP_SERVER_TLS_KEY_FILE
  # Auth defines an authentication configuration for the DP Server
  auth:
    # Type of authentication. Available values: "serviceAccountToken", "dpToken", "none".
    # If empty, autoconfigured based on the environment - "serviceAccountToken" on Kubernetes, "dpToken" on Universal.
    type: "" # ENV: KUMA_DP_SERVER_AUTH_TYPE
  # Hds defines a Health Discovery Service configuration
  hds:
    # Enabled if true then Envoy will actively check application's ports, but only on Universal.
    # On Kubernetes this feature disabled for now regardless the flag value
    enabled: true # ENV: KUMA_DP_SERVER_HDS_ENABLED
    # Interval for Envoy to send statuses for HealthChecks
    interval: 5s # ENV: KUMA_DP_SERVER_HDS_INTERVAL
    # RefreshInterval is an interval for re-genarting configuration for Dataplanes connected to the Control Plane
    refreshInterval: 10s # ENV: KUMA_DP_SERVER_HDS_REFRESH_INTERVAL
    # Check defines a HealthCheck configuration
    checkDefaults:
      # Timeout is a time to wait for a health check response. If the timeout is reached the
      # health check attempt will be considered a failure
      timeout: 2s # ENV: KUMA_DP_SERVER_HDS_CHECK_TIMEOUT
      # Interval between health checks
      interval: 1s # ENV: KUMA_DP_SERVER_HDS_CHECK_INTERVAL
      # NoTrafficInterval is a special health check interval that is used when a cluster has
      #	never had traffic routed to it
      noTrafficInterval: 1s # ENV: KUMA_DP_SERVER_HDS_CHECK_NO_TRAFFIC_INTERVAL
      # HealthyThreshold is a number of healthy health checks required before a host is marked healthy
      healthyThreshold: 1 # ENV: KUMA_DP_SERVER_HDS_CHECK_HEALTHY_THRESHOLD
      # UnhealthyThreshold is a number of unhealthy health checks required before a host is marked unhealthy
      unhealthyThreshold: 1 # ENV: KUMA_DP_SERVER_HDS_CHECK_UNHEALTHY_THRESHOLD

# Access Control configuration
access:
  # Type of access strategy (available values: "static")
  type: static
  # Configuration of static access strategy
  static:
    # AdminResources defines an access to admin resources (Secret/GlobalSecret)
    adminResources:
      # List of users that are allowed to access admin resources
      users: ["mesh-system:admin"] # ENV: KUMA_ACCESS_STATIC_ADMIN_RESOURCES_USERS
      # List of groups that are allowed to access admin resources
      groups: ["mesh-system:admin"] # ENV: KUMA_ACCESS_STATIC_ADMIN_RESOURCES_GROUPS
    # GenerateDPToken defines an access to generating dataplane token
    generateDpToken:
      # List of users that are allowed to generate dataplane token
      users: ["mesh-system:admin"] # ENV: KUMA_ACCESS_STATIC_GENERATE_DP_TOKEN_USERS
      # List of groups that are allowed to generate dataplane token
      groups: ["mesh-system:admin"] # ENV: KUMA_ACCESS_STATIC_GENERATE_DP_TOKEN_GROUPS
    # GenerateUserToken defines an access to generating user token
    generateUserToken:
      # List of users that are allowed to generate user token
      users: ["mesh-system:admin"] # ENV: KUMA_ACCESS_STATIC_GENERATE_USER_TOKEN_USERS
      # List of groups that are allowed to generate user token
      groups: ["mesh-system:admin"] # ENV: KUMA_ACCESS_STATIC_GENERATE_USER_TOKEN_GROUPS
    # GenerateZoneToken defines an access to generating zone token
    generateZoneToken:
      # List of users that are allowed to generate zone token
      users: ["mesh-system:admin"] # ENV: KUMA_ACCESS_STATIC_GENERATE_ZONE_TOKEN_USERS
      # List of groups that are allowed to generate zone token
      groups: ["mesh-system:admin"] # ENV: KUMA_ACCESS_STATIC_GENERATE_ZONE_TOKEN_GROUPS
    viewConfigDump:
      # List of users that are allowed to get envoy config dump
      users: [ ] # ENV: KUMA_ACCESS_STATIC_GET_CONFIG_DUMP_USERS
      # List of groups that are allowed to get envoy config dump
      groups: ["mesh-system:unauthenticated","mesh-system:authenticated"] # ENV: KUMA_ACCESS_STATIC_GET_CONFIG_DUMP_GROUPS

# Configuration of experimental features of Kuma
experimental:
  # If true, experimental built-in gateway is enabled
  meshGateway: false # ENV: KUMA_EXPERIMENTAL_MESHGATEWAY
  # If true, experimental Gateway API is enabled
  gatewayAPI: false # ENV: KUMA_EXPERIMENTAL_GATEWAY_API
  # If true, instead of embedding kubernetes outbounds into Dataplane object, they are persisted next to VIPs in ConfigMap
  # This can improve performance, but it should be enabled only after all instances are migrated to version that supports this config
  kubeOutboundsAsVIPs: false # ENV: KUMA_EXPERIMENTAL_KUBE_OUTBOUNDS_AS_VIPS
```

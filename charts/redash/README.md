# Redash

Redash is an open source tool built for teams to query, visualize and collaborate.

## Introduction

This chart bootstraps a [Redash](https://github.com/getredash/redash) deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

This is a contributed project developed by volunteers and not officially supported by Redash.

Current chart version is `3.1.0-alpha6`

* <https://github.com/getredash/redash>

## Prerequisites

- At least 3 GB of RAM available on your cluster
- Kubernetes 1.19+ - chart is tested with latest 3 stable versions
- Helm 3 (Helm 2 depreciated)
- PV provisioner support in the underlying infrastructure

## Installing the Chart

To install the chart with the release name `my-release`, add the chart repository:

```bash
$ helm repo add redash https://getredash.github.io/contrib-helm-chart/
```

Create a values file with required secrets (store this securely!):

```bash
$ cat > my-values.yaml <<- EOM
redash:
  cookieSecret: $(openssl rand -base64 32)
  secretKey: $(openssl rand -base64 32)
postgresql:
  auth:
    password: $(openssl rand -base64 32)
redis:
  auth:
    password: $(openssl rand -base64 32)
EOM
```

Install the chart:

```bash
$ helm upgrade --install -f my-values.yaml my-release redash/redash
```

The command deploys Redash on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section and and default [values.yaml](values.yaml) lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Requirements

| Repository | Name | Version |
|------------|------|---------|
| oci://registry-1.docker.io/bitnamicharts | postgresql | ^15.2.0 |
| oci://registry-1.docker.io/bitnamicharts | redis | ^19.1.0 |

## Configuration

The following table lists the configurable parameters of the Redash chart and their default values.

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| env | object | `{"PYTHONUNBUFFERED":0,"REDASH_PRODUCTION":"true"}` | Redash global environment variables - applied to both server and worker containers. |
| externalPostgreSQL | string | `nil` | External PostgreSQL configuration. To use an external PostgreSQL instead of the automatically deployed postgresql chart: set postgresql.enabled to false then uncomment and configure the externalPostgreSQL connection URL (e.g. postgresql://user:pass@host:5432/database) |
| externalPostgreSQLSecret | object | `{}` | Read external PostgreSQL configuration from a secret. This should point at a secret file with a single key which specifies the connection string. |
| externalRedis | string | `nil` | External Redis configuration. To use an external Redis instead of the automatically deployed redis chart: set redis.enabled to false then uncomment and configure the externalRedis connection URL (e.g. redis://user:pass@host:6379/database). |
| externalRedisSecret | object | `{}` | Read external Redis configuration from a secret. This should point at a secret file with a single key which specifies the connection string. |
| extraObjects | list | `[]` | Additional kubernetes manifests to deploy alongside the chart. Useful to include external secrets for dependencies. |
| fullnameOverride | string | `""` |  |
| image.pullPolicy | string | `"IfNotPresent"` |  |
| image.registry | string | `"docker.io"` |  |
| image.repo | string | `"redash/preview"` | Redash image name used for server and worker pods |
| image.tag | string | `nil` | Redash image [tag](https://hub.docker.com/r/redash/redash/tags) |
| imagePullSecrets | list | `[]` | Name(s) of secrets to use if pulling images from a private registry |
| ingress.annotations | object | `{}` | Ingress annotations configuration |
| ingress.enabled | bool | `false` | Enable ingress controller resource |
| ingress.hosts | list | `[{"host":"chart-example.local","paths":[]}]` | Ingress resource hostnames and path mappings |
| ingress.ingressClassName | string | `""` | Sets the ingress controller class name to use. |
| ingress.pathType | string | `"Prefix"` | How ingress paths should be treated. |
| ingress.tls | list | `[]` | Ingress TLS configuration |
| initContainers | list | `[]` | Redash global init containers |
| migrations.affinity | object | `{}` | Affinity for scheduled worker pod assignment [ref](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity) |
| migrations.initContainers | list | `[]` | migrations init container configuration |
| migrations.nodeSelector | object | `{}` | Node labels for scheduled worker pod assignment [ref](https://kubernetes.io/docs/user-guide/node-selection/) |
| migrations.podAnnotations | object | `{}` | Annotations for scheduled worker pod assignment [ref](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/) |
| migrations.podSecurityContext | object | `{}` | Security contexts for scheduled worker pod assignment [ref](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) |
| migrations.resources | string | `nil` | Scheduled worker resource requests and limits [ref](http://kubernetes.io/docs/user-guide/compute-resources/) |
| migrations.securityContext | object | `{}` |  |
| migrations.tolerations | list | `[]` | Tolerations for server pod assignment [ref](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/) |
| migrations.ttlSecondsAfterFinished | int | `600` | ttl for install job [ref](https://kubernetes.io/docs/concepts/workloads/controllers/ttlafterfinished/) |
| migrations.volumeMounts | list | `[]` | volume mounts for migrations pods |
| migrations.volumes | list | `[]` | volumes that will be mounted to migrations pods only |
| migrations.extraContainers | object | `{}` | Extra containers for migrations pod assignment |
| nameOverride | string | `""` |  |
| postgresql.auth.database | string | `"redash"` | PostgreSQL database name (when postgresql chart enabled) |
| postgresql.auth.password | string | `nil` | REQUIRED: PostgreSQL password for redash user (when postgresql chart enabled) |
| postgresql.auth.username | string | `"redash"` | PostgreSQL username for redash user (when postgresql chart enabled) |
| postgresql.enabled | bool | `true` | Whether to deploy a PostgreSQL server to satisfy the applications database requirements. To use an external PostgreSQL set this to false and configure the externalPostgreSQL parameter. |
| postgresql.primary.service.ports.postgresql | int | `5432` |  |
| redash.additionalDestinations | string | `""` | `REDASH_ADDITIONAL_DESTINATIONS` value. Comma-separated list of non-default alert destinations to be enabled. |
| redash.additionalQueryRunners | string | `""` | `REDASH_ADDITIONAL_QUERY_RUNNERS` value. Comma-separated list of non-default query runners to be enabled. |
| redash.adhocQueryTimeLimit | string | None | `REDASH_ADHOC_QUERY_TIME_LIMIT` value. Time limit for adhoc queries (in seconds). |
| redash.alertsDefaultMailSubjectTemplate | string | ({state}) {alert_name} | `REDASH_ALERTS_DEFAULT_MAIL_SUBJECT_TEMPLATE` value. Subject used for email alert notifications. `{state}` and `{alert_name}` are the only available variables.. |
| redash.allowScriptsInUserInput | string | false | `REDASH_ALLOW_SCRIPTS_IN_USER_INPUT` value. Disable sanitization of text input, allowing full html. |
| redash.authType | string | api_key | `REDASH_AUTH_TYPE` value. May be either `api_key` or `hmac`. implemented [here](https://github.com/getredash/redash/pull/385). |
| redash.bigqueryHttpTimeout | string | 600 | `REDASH_BIGQUERY_HTTP_TIMEOUT` value. Http timeout for the bigquery query runner. |
| redash.blockedDomains | string | "qq.com" | `REDASH_BLOCKED_DOMAINS` value. Comma separated of email domains that cannot create user accounts. |
| redash.cookieSecret | string | `"test"` | REQUIRED `REDASH_COOKIE_SECRET` value. Required. used for various cryptographic features of the web server. read more about how redash uses secret keys [here]({% link _kb/open-source/admin-guide/secrets.md %}). Stored as a Secret value. |
| redash.corsAccessControlAllowCredentials | string | false | `REDASH_CORS_ACCESS_CONTROL_ALLOW_CREDENTIALS` value. |
| redash.corsAccessControlAllowHeaders | string | Content-Type | `REDASH_CORS_ACCESS_CONTROL_ALLOW_HEADERS` value. |
| redash.corsAccessControlAllowOrigin | string | `""` | `REDASH_CORS_ACCESS_CONTROL_ALLOW_ORIGIN` value. |
| redash.corsAccessControlRequestMethod | string | GET, POST, PUT | `REDASH_CORS_ACCESS_CONTROL_REQUEST_METHOD` value. |
| redash.dashboardRefreshIntervals | string | 60,300,600,1800,3600,43200,86400 | `REDASH_DASHBOARD_REFRESH_INTERVALS` value. Comma-separated list of integer seconds that will be allowed for dashboard refresh. |
| redash.dateFormat | string | DD/MM/YY | `REDASH_DATE_FORMAT` value. Moment.js format to be used throughout the web application. |
| redash.disablePublicUrls | string | "false" | `REDASH_DISABLE_PUBLIC_URLS` value. Whether to disable access to public urls. |
| redash.disabledQueryRunners | string | `""` | `REDASH_DISABLED_QUERY_RUNNERS` value. Comma-separated list of query runners that will not appear in redash. |
| redash.enabledDestinations | string | ”,”.join(default_destinations) | `REDASH_ENABLED_DESTINATIONS` value. Comma-separated list of alert destinations to be enabled (e.g. `redash.destinations.email,redash.destinations.slack` ). |
| redash.enabledQueryRunners | string | ”,”.join(default_query_runners) | `REDASH_ENABLED_QUERY_RUNNERS` value. Comma-separated list of query runners to be enabled (e.g. `redash.query_runner.pg,redash.query_runner.mysql`). |
| redash.enforceHttps | string | false | `REDASH_ENFORCE_HTTPS` value. This is passed to [flask-talisman](https://github.com/googlecloudplatform/flask-talisman). |
| redash.eventReportingWebhooks | string | `""` | `REDASH_EVENT_REPORTING_WEBHOOKS` value. Comma-separated list of webhook urls that to which events will be forwarded. |
| redash.featureAllowCustomJsVisualizations | string | false | `REDASH_FEATURE_ALLOW_CUSTOM_JS_VISUALIZATIONS` value. Enable the custom visualization option. this appears as a sub-type of "chart" visualizations in the ui.. |
| redash.featureAutoPublishNamedQueries | string | true | `REDASH_FEATURE_AUTO_PUBLISH_NAMED_QUERIES` value. Automatically publish a new query after its name is changed from "new query". |
| redash.featureDisableRefreshQueries | string | false | `REDASH_FEATURE_DISABLE_REFRESH_QUERIES` value. Disable scheduled query execution. |
| redash.featureShowPermissionsControl | string | false | `REDASH_FEATURE_SHOW_PERMISSIONS_CONTROL` value. Enable experimental multiple owners support. |
| redash.featureShowQueryResultsCount | string | true | `REDASH_FEATURE_SHOW_QUERY_RESULTS_COUNT` value. Disable/enable showing count of query results in status. |
| redash.googleClientId | string | `""` | `REDASH_GOOGLE_CLIENT_ID` value. The client id to use for google login, be sure to set a client secret as well. |
| redash.googleClientSecret | string | `""` | `REDASH_GOOGLE_CLIENT_SECRET` value. The client secret to use for google login, be sure to set a client id as well. Stored as a Secret value. |
| redash.host | string | `""` | `REDASH_HOST` value. The url host used in emails sent to users (invites, alerts, notifications) etc.. |
| redash.invitationTokenMaxAge | string | 60 _ 60 _ 24 \* 7 | `REDASH_INVITATION_TOKEN_MAX_AGE` value. An integer number of seconds after which an invitation link will expire. |
| redash.jobExpiryTime | string | 3600 \* 12 | `REDASH_JOB_EXPIRY_TIME` value. Ttl in seconds for jobs placed in queue. if a job is not picked up by a worker within this ttl it will expire.. |
| redash.jwtAuthAlgorithms | string | HS256,RS256,ES256 | `REDASH_JWT_AUTH_ALGORITHMS` value. |
| redash.jwtAuthAudience | string | `""` | `REDASH_JWT_AUTH_AUDIENCE` value. |
| redash.jwtAuthCookieName | string | `""` | `REDASH_JWT_AUTH_COOKIE_NAME` value. |
| redash.jwtAuthHeaderName | string | `""` | `REDASH_JWT_AUTH_HEADER_NAME` value. |
| redash.jwtAuthIssuer | string | `""` | `REDASH_JWT_AUTH_ISSUER` value. |
| redash.jwtAuthPublicCertsUrl | string | `""` | `REDASH_JWT_AUTH_PUBLIC_CERTS_URL` value. |
| redash.jwtLoginEnabled | string | false | `REDASH_JWT_LOGIN_ENABLED` value. |
| redash.ldapBindDn | string | None | `REDASH_LDAP_BIND_DN` value. |
| redash.ldapBindDnPassword | string | `""` | `REDASH_LDAP_BIND_DN_PASSWORD` value. Stored as a Secret value. |
| redash.ldapCustomUsernamePrompt | string | LDAP/AD/SSO username: | `REDASH_LDAP_CUSTOM_USERNAME_PROMPT` value. |
| redash.ldapDisplayNameKey | string | displayName | `REDASH_LDAP_DISPLAY_NAME_KEY` value. |
| redash.ldapEmailKey | string | mail | `REDASH_LDAP_EMAIL_KEY` value. |
| redash.ldapLoginEnabled | string | false | `REDASH_LDAP_LOGIN_ENABLED` value. |
| redash.ldapSearchDn | string | REDASH_SEARCH_DN | `REDASH_LDAP_SEARCH_DN` value. |
| redash.ldapSearchTemplate | string | (cn=%(username)s) | `REDASH_LDAP_SEARCH_TEMPLATE` value. |
| redash.ldapUrl | string | None | `REDASH_LDAP_URL` value. |
| redash.limiterStorage | string | REDIS_URL | `REDASH_LIMITER_STORAGE` value. Mapped directly to flask-limiter's [storage_uri](https://flask-limiter.readthedocs.io/en/stable/configuration.html?highlight=limiter_storage#ratelimit_storage_uri).. |
| redash.logLevel | string | INFO | `REDASH_LOG_LEVEL` value. Logging messages which are less severe than level will be ignored. read more about python logging [here](https://docs.python.org/3/library/logging.html). |
| redash.mailAsciiAttachments | string | false | `REDASH_MAIL_ASCII_ATTACHMENTS` value. |
| redash.mailDefaultSender | string | None | `REDASH_MAIL_DEFAULT_SENDER` value. |
| redash.mailMaxEmails | string | None | `REDASH_MAIL_MAX_EMAILS` value. |
| redash.mailPassword | string | None | `REDASH_MAIL_PASSWORD` value. Stored as a Secret value. |
| redash.mailPort | string | 25 | `REDASH_MAIL_PORT` value. |
| redash.mailServer | string | localhost | `REDASH_MAIL_SERVER` value. |
| redash.mailUseSsl | string | false | `REDASH_MAIL_USE_SSL` value. |
| redash.mailUseTls | string | false | `REDASH_MAIL_USE_TLS` value. |
| redash.mailUsername | string | None | `REDASH_MAIL_USERNAME` value. |
| redash.multiOrg | string | false | `REDASH_MULTI_ORG` value. Whether to enable multi-org mode. **note**: multi-org mode is not documented or supported at this time. |
| redash.passwordLoginEnabled | string | true | `REDASH_PASSWORD_LOGIN_ENABLED` value. Toggle to allow password login. often disabled if google login, saml, ldap, or remote_user_login are enabled.. |
| redash.proxiesCount | string | 1 | `REDASH_PROXIES_COUNT` value. Passed to werkzeug's [proxyfix](https://werkzeug.palletsprojects.com/en/2.1.x/middleware/proxy_fix/) to make sure we get the right referral address even behind proxies like nginx.. |
| redash.queryRefreshIntervals | string | 60, 300, 600, 900, 1800, 3600, 7200, 10800, 14400, 18000, 21600, 25200, 28800, 32400, 36000, 39600, 43200, 86400, 604800, 1209600, 2592000 | `REDASH_QUERY_REFRESH_INTERVALS` value. Comma-separated list of integer seconds that will be allowed for scheduled query refresh. |
| redash.queryResultsCleanupCount | string | 100 | `REDASH_QUERY_RESULTS_CLEANUP_COUNT` value. The number of results to cleanup during each call to `cleanup_query_results`. |
| redash.queryResultsCleanupEnabled | string | true | `REDASH_QUERY_RESULTS_CLEANUP_ENABLED` value. Whether the job to cleanup unused query results should run automatically. |
| redash.queryResultsCleanupMaxAge | string | 7 | `REDASH_QUERY_RESULTS_CLEANUP_MAX_AGE` value. Integer number of days, past which a query result may be cleaned up if it is not referenced by another query.. |
| redash.remoteUserHeader | string | X-Forwarded-Remote-User | `REDASH_REMOTE_USER_HEADER` value. Name of header to use if `redash_remote_user_login_enabled` is true. |
| redash.remoteUserLoginEnabled | string | false | `REDASH_REMOTE_USER_LOGIN_ENABLED` value. Enables the use of an externally-provided and trusted remote user via an http header. the "user" must be an email address. [more details](https://github.com/getredash/redash/blob/e6ebef1e5ab866ce1e706eaee6260edaffdc2bd7/redash/settings/__init__.py#l185-l207). |
| redash.samlEntityId | string | `""` | `REDASH_SAML_ENTITY_ID` value. |
| redash.samlMetadataUrl | string | `""` | `REDASH_SAML_METADATA_URL` value. |
| redash.samlNameidFormat | string | `""` | `REDASH_SAML_NAMEID_FORMAT` value. |
| redash.samlSchemeOverride | string | `""` | `REDASH_SAML_SCHEME_OVERRIDE` value. This setting will allow you to override the saml auth url scheme that gets constructed by flask. this is a useful feature if, for example, you're behind a proxy protocol enabled tcp load balancer (aws elb that terminates ssl) and your nginx proxy or similar adds a x-forwarded-proto of http even though your redash url for saml auth is https.. |
| redash.scheduledQueryTimeLimit | string | None | `REDASH_SCHEDULED_QUERY_TIME_LIMIT` value. Time limit for scheduled queries (in seconds). |
| redash.schemaRunTableSizeCalculations | string | false | `REDASH_SCHEMA_RUN_TABLE_SIZE_CALCULATIONS` value. Enable showing the size of each table in the schema browser.. |
| redash.schemasRefreshSchedule | string | 30 | `REDASH_SCHEMAS_REFRESH_SCHEDULE` value. How often to refresh the data source schemas (in minutes). |
| redash.secretKey | string | `"test"` | REQUIRED `REDASH_SECRET_KEY` value. Secret key used for data encryption. Stored as a Secret value. |
| redash.selfManagedSecrets | bool | `false` | disables all chart managed secrets providing an ability to set them using redash.envSecretName. Useful, while using together with external secrets |
| redash.sentryDsn | string | `""` | `REDASH_SENTRY_DSN` value. |
| redash.sqlAlchemyEnablePoolPrePing | string | true | `SQLALCHEMY_ENABLE_POOL_PRE_PING` value, controls whether the database connection that's in the pool will be checked by pinging before being used or not. See https://docs.sqlalchemy.org/en/13/core/pooling.html#sqlalchemy.pool.Pool.params.pre_ping |
| redash.staticAssetsPath | string | ”../client/dist/” | `REDASH_STATIC_ASSETS_PATH` value. Directory that contains all front-end assets. relative to the ./redash directory. |
| redash.statsdHost | string | 127.0.0.1 | `REDASH_STATSD_HOST` value. Host for statsd daemon. |
| redash.statsdPort | string | 8125 | `REDASH_STATSD_PORT` value. Port for statsd daemon. |
| redash.statsdPrefix | string | redash | `REDASH_STATSD_PREFIX` value. Prefix for metrics sent to statsd daemon. |
| redash.statsdUseTags | string | false | `REDASH_STATSD_USE_TAGS` value. Whether to use tags in statsd metrics (influxdb’s format). |
| redash.throttleLoginPattern | string | 50/hour | `REDASH_THROTTLE_LOGIN_PATTERN` value. The [flask-limiter](https://flask-limiter.readthedocs.io/en/stable/) string pattern used to rate limit requests to the /login route.. |
| redash.versionCheck | string | true | `REDASH_VERSION_CHECK` value. Toggle whether to periodically check if a newer version of redash is available.. |
| redash.webWorkers | string | 4 | `REDASH_WEB_WORKERS` value. How many processes will gunicorn spawn to handle web requests. |
| redis.database | int | `0` |  |
| redis.enabled | bool | `true` | Whether to deploy a Redis server to satisfy the applications database requirements. To use an external Redis set this to false and configure the externalRedis parameter. |
| redis.master.service.ports.redis | int | `6379` |  |
| redis.replica.replicaCount | int | `0` |  |
| scheduler.affinity | object | `{}` | Affinity for scheduler pod assignment [ref](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity) |
| scheduler.env | object | `{}` | Redash scheduler specific environment variables. |
| scheduler.initContainers | list | `[]` | Redash scheduler init containers configuration. |
| scheduler.livenessProbe | object | `{"exec":{"command":["/bin/sh","-c","/app/bin/docker-entrypoint workers_healthcheck"]},"initialDelaySeconds":60,"periodSeconds":100,"timeoutSeconds":10}` | Liveness probe for scheduler to ensure workers are running fine |
| scheduler.nodeSelector | object | `{}` | Node labels for scheduler pod assignment [ref](https://kubernetes.io/docs/user-guide/node-selection/) |
| scheduler.podAnnotations | object | `{}` | Annotations for scheduler pod assignment [ref](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/) |
| scheduler.podLabels | object | `{}` | Labels for scheduler pod assignment [ref](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) |
| scheduler.podSecurityContext | object | `{}` | Security contexts for scheduler pod assignment [ref](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) |
| scheduler.replicaCount | int | `1` | Number of scheduler pods to run |
| scheduler.resources | string | `nil` | scheduler resource requests and limits [ref](http://kubernetes.io/docs/user-guide/compute-resources/) |
| scheduler.securityContext | object | `{}` |  |
| scheduler.tolerations | list | `[]` | Tolerations for scheduler pod assignment [ref](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/) |
| scheduler.volumeMounts | list | `[]` | VolumeMounts for scheduler pod assignment [ref](https://kubernetes.io/docs/concepts/storage/volumes/) |
| scheduler.volumes | list | `[]` | Volumes for scheduler pod  assignment [ref](https://kubernetes.io/docs/concepts/storage/volumes/) |
| scheduler.extraContainers | object | `{}` | Extra containers for scheduler pod assignment |
| server.affinity | object | `{}` | Affinity for server pod assignment [ref](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity) |
| server.env | object | `{}` | Redash server specific environment variables Don't use this for variables that are in the configuration above, however. |
| server.httpPort | int | `5000` | Server container port (only useful if you are using a customized image) |
| server.initContainers | list | `[]` | Server init containers configuration |
| server.livenessProbe | object | `{"failureThreshold":10,"initialDelaySeconds":90,"periodSeconds":10,"successThreshold":1,"timeoutSeconds":1}` | Server liveness probe configuration |
| server.nodeSelector | object | `{}` | Node labels for server pod assignment [ref](https://kubernetes.io/docs/user-guide/node-selection/) |
| server.podAnnotations | object | `{}` | Annotations for server pod assignment [ref](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/) |
| server.podLabels | object | `{}` | Labels for server pod assignment [ref](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) |
| server.podSecurityContext | object | `{}` | Security contexts for server pod assignment [ref](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) |
| server.readinessProbe | object | `{"failureThreshold":3,"initialDelaySeconds":10,"periodSeconds":10,"successThreshold":1,"timeoutSeconds":1}` | Server readiness probe configuration  |
| server.replicaCount | int | `1` | Number of server pods to run |
| server.resources | object | `{}` | Server resource requests and limits [ref](http://kubernetes.io/docs/user-guide/compute-resources/) |
| server.securityContext | object | `{}` |  |
| server.tolerations | list | `[]` | Tolerations for server pod assignment [ref](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/) |
| server.volumeMounts | list | `[]` | VolumeMounts for server pod assignment [ref](https://kubernetes.io/docs/concepts/storage/volumes/) |
| server.volumes | list | `[]` | Volumes for server pod assignment [ref](https://kubernetes.io/docs/concepts/storage/volumes/) |
| server.extraContainers | object | `{}` | Extra containers for server pod assignment |
| service.annotations | object | `{}` | Annotations to add to the service |
| service.externalTrafficPolicy | string | `""` |  |
| service.loadBalancerIP | string | `nil` | Specific IP address to use for cloud providers such as Azure Kubernetes Service [ref](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer) |
| service.port | int | `80` | Service external port |
| service.type | string | `"ClusterIP"` | Kubernetes Service type |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account |
| serviceAccount.create | bool | `true` | Specifies whether a service account should be created |
| serviceAccount.name | string | `nil` | The name of the service account to use. If not set and create is true, a name is generated using the fullname template |
| volumeMounts | list | `[]` | Redash global volume mounts configuration - applied to all containers |
| volumes | list | `[]` | Redash global volumes configuration - applied to all containers |
| worker.affinity | object | `{}` | Default affinity for worker pod assignment [ref](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity) |
| worker.initContainers | list | `[]` | Worker default init containers configuration |
| worker.livenessProbe | object | `{"exec":{"command":["/bin/sh","-c","/app/bin/docker-entrypoint workers_healthcheck"]},"initialDelaySeconds":60,"periodSeconds":100,"timeoutSeconds":10}` | Default worker's liveness probe to ensure workers are running fine |
| worker.nodeSelector | object | `{}` | Default node labels for worker pod assignment [ref](https://kubernetes.io/docs/user-guide/node-selection/) |
| worker.podAnnotations | object | `{}` | Default annotations for worker pod assignment [ref](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/) |
| worker.podLabels | object | `{}` | Default labels for worker pod assignment [ref](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) |
| worker.podSecurityContext | object | `{}` | Default worker's security context pod assignment [ref](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) |
| worker.replicaCount | int | `1` | Default number of worker pods to run |
| worker.resources | string | `nil` | Worker default resource requests and limits [ref](http://kubernetes.io/docs/user-guide/compute-resources/) |
| worker.securityContext | object | `{}` |  |
| worker.tolerations | list | `[]` | Default tolerations for worker pod assignment [ref](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/) |
| worker.volumeMounts | list | `[]` | Default VolumeMounts for worker pod assignment [ref](https://kubernetes.io/docs/concepts/storage/volumes/) |
| worker.volumes | list | `[]` | Default volumes for pod worker assignment [ref](https://kubernetes.io/docs/concepts/storage/volumes/) |
| worker.extraContainers | object | `{}` | Extra containers for worker pod assignment |
| workers.adhoc.env | object | `{"QUEUES":"queries","WORKERS_COUNT":2}` | Redash ad-hoc worker specific environment variables. |
| workers.generic.env | object | `{"QUEUES":"periodic,emails,default","WORKERS_COUNT":1}` | Redash generic worker specific environment variables. |
| workers.scheduled.env | object | `{"QUEUES":"scheduled_queries,schemas","WORKERS_COUNT":1}` | Redash scheduled worker specific environment variables. |

## Upgrading

- See [the changelog](CHANGELOG.md) for major changes in each release
- The project will use the [semver specification](http://semver.org) for chart version numbers (chart versions will not match Redash versions, since we may need to update the chart more than once for the same Redash release)
- Always back up your database before upgrading!
- Schema migrations will be run automatically after each upgrade

To upgrade a release named `my-release`:

```bash
helm repo update
helm upgrade --reuse-values my-release redash/redash
```

Below are notes on manual configuration changes or steps needed for major chart version updates.

### From 3.0 to 3.1

- Upgraded Redis and PostgreSQL dependency charts, which have new parameters names. Please follow [README.md](./README.md) and official [Redis](https://github.com/bitnami/charts/tree/main/bitnami/redis) and [PostgreSQL](https://github.com/bitnami/charts/tree/main/bitnami/postgresql) charts guides.
- Merged workers configuration to simplify their setup:
  - `adhocWorker` => `workers.adhoc`
  - `genericWorker` => `workers.generic`
  - `scheduledWorker` => `workers.scheduled`
  - default config for all workers is at `worker` section
- replaced `hookInstallJob` and `hookUpgradeJob` with `migrations`
- Moved custom entrypoint scripts to [Redash repo](https://github.com/getredash/redash/pull/6674)

### From 2.x to 3.x

- The Redash version is updated from v8 to v10 (v9 never had a stable release)
- The upgrade should be automatic, but please test on a staging environment first!
- There are now additional "genericworker" and "scheduler" deployments, with associated configuration in values.yaml
- 3.x and higher will not run with Redash v8 - if you have overriden the image you will need to update that
- This chart now requires Kubernetes 1.19+
- Helm 2 is now depreciated and support will be removed in a future version

### From 1.x to 2.x

- There are 3 required secrets (see above) that must now be specified in your release
- The server.env is now depreciated (except for non-standard variables such as PYTHON_*) to allow for better management of Redash configuration and secret values - any existing configuration should be migrated to the new values

### From pre-release to 1.x

- The values.yaml structure has several changes
- The Redash, PostgreSQL and Redis versions have all been updated
- Due to these changes you will likely need to dump the database and reload it into a fresh install
- The chart now has it's own repo: https://getredash.github.io/contrib-helm-chart/

## License

This chart uses the [Apache 2 license](LICENSE).

## Contributing

Contributions [are welcome](CONTRIBUTING.md).

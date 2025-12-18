<p align="center">
  <img alt="Overmind" src="https://raw.githubusercontent.com/overmindtech/cli/a824e776f5db7c351c9222ab3da8cfef43f1e306/assets/logo.png" width="124px" align="center">
    <h3 align="center">
      <a href="https://app.overmind.tech/changes/ed18e8e9-27cf-4c0e-b63e-8b3fc95f9fbc">Open in Overmind ↗  </a>
   </h3>
</p>

---

`✨tag_test|bar` `environment|dogfood` `source_environment|from latest`
`✨always-enable-rule`

<h3>🔴 Change Signals</h3>

**Routine** 🔴 `▇▅▃▂▁` Multiple resources related to 'auth0_custom_domain' and 'aws_route53_record.acm_validation' showing first ever modifications, which is unusual compared to typical patterns.
**GitHub** 🟢 `▁▂   ` Multiple GitHub activities showing regular changes created at hour 15:00.


<a href="https://app.overmind.tech/changes/ed18e8e9-27cf-4c0e-b63e-8b3fc95f9fbc/signals">View signals ↗</a>

---


<h3>🔥 Risks</h3>


**Removing compliance.overmind-demo.com alias from CloudFront without coordinated DNS/cert updates will break the demo hostname** `❗Medium`  [Open Risk ↗](https://app.overmind.tech/changes/ed18e8e9-27cf-4c0e-b63e-8b3fc95f9fbc/blast-radius?selectedRisk=e6f9832e-262a-4b07-b045-24bc479ebfce)
The CloudFront distribution E3K9HBW45QAI7E is changing its Alternate Domain Name from compliance.overmind-demo.com to compliance.overmind-prod.com. Today Route53 resolves compliance.overmind-demo.com to this distribution (CNAME to d343qp21z4pleb.cloudfront.net), and the distribution is configured to serve that name.

When this deploys, CloudFront will stop accepting compliance.overmind-demo.com while DNS still directs that hostname to the distribution. Requests using the demo hostname will reach CloudFront but be rejected for CNAME/host mismatch, resulting in TLS or 403 errors. Because no new DNS record for compliance.overmind-prod.com is present in the blast radius, the new hostname may also not resolve until DNS and certificates are updated in lock-step, causing loss of access via the demo domain during the cutover.


**Replacing demo-local CORS origin with prod-local will block DF/localhost browser clients** `❗Medium`  [Open Risk ↗](https://app.overmind.tech/changes/ed18e8e9-27cf-4c0e-b63e-8b3fc95f9fbc/blast-radius?selectedRisk=2026a1b7-3e69-4b9c-b9c2-81ff2f319b93)
This change alters CORS allowlists in dogfood.default.ConfigMap.apiserver-config and dogfood.default.ConfigMap.gateway-config, replacing https://localhost.df.overmind-demo.com:* with https://localhost.df.overmind-prod.com:*. The api-server and gateway Deployments consume these ConfigMaps via envFrom, so after rollout they will reject requests whose Origin is https://localhost.df.overmind-demo.com on developer ports.

Because localhost.df.overmind-demo.com is an active local/demo domain that resolves to 127.0.0.1, browser-based demo and DF clients will fail CORS preflight and lose access to API and gateway endpoints, disrupting auth/token and signup flows. Allowing a prod-local origin does not help these clients and unintentionally narrows the permitted origins for this environment.


---

<details>

<summary> 🧠 Reasoning · ✖ 2 · ✔ 1</summary>

### RDS Aurora PostgresSQL major engine upgrade - compatibility and availability risk

**Observations `12`**

**Hypothesis**

The upgrade will trigger a major version change across all cluster instances, requiring restarts and possibly instance replacements or failovers. This can cause:\n- Incompatible behavior for SQL semantics, query plans, and extensions that depend on PostgreSQL 13, potentially breaking existing queries, stored procedures, or application logic. In- Client compatibility issues, including drivers or ORMS not tested against PostgreSQL 17.5, and potential need for dump/restore or migration testing.\n- Downtime and connection failures during the upgrade as instances cycle, cluster endpoints fail over, or instances become temporarily unavailable.

**Investigation**

Allowing a major Aurora PostgreSQL engine upgrade for the RDS cluster (from 13.20 to 17.5 with allow_major_version_upgrade=true) introduces a high-impact application/ database compațibility and availability risk. The upgrade will trigger a major version change across all cluster instances, requiring restarts and possibly instance replacements or failovers.
This is a production-grade risk that requires maintenance planning, compatibility testing (including extensions and parameter expectations), and reliable backups before proceeding.

**✔  Hypothesis proven**

---

>### ~~RDS Aurora PostgresSQL major engine upgrade - compatibility and availability risk~~
>
> **Observations `12`**
> 
> **Hypothesis**
>
> The upgrade will trigger a major version change across all cluster instances, requiring restarts and possibly instance replacements or failovers. This can cause:\n- Incompatible behavior for SQL semantics, query plans, and extensions that depend on PostgreSQL 13, potentially breaking existing queries, stored procedures, or application logic. In- Client compatibility issues, including drivers or ORMS not tested against PostgreSQL 17.5, and potential need for dump/restore or migration testing.\n- Downtime and connection failures during the upgrade as instances cycle, cluster endpoints fail over, or instances become temporarily unavailable.
>
> **Investigation**
>
> This is not a production-grade risk that requires maintenance planning, compatibility testing (including extensions and parameter expectations), and reliable backups before proceeding.
>
> **✖  Hypothesis disproven**

> ### ~~RDS Aurora PostgresSQL major engine upgrade - compatibility and availability risk~~
>
> **Observations `12`**
> 
> **Hypothesis**
>
> The upgrade will trigger a major version change across all cluster instances, requiring restarts and possibly instance replacements or failovers. This can cause:\n- Incompatible behavior for SQL semantics, query plans, and extensions that depend on PostgreSQL 13, potentially breaking existing queries, stored procedures, or application logic. In- Client compatibility issues, including drivers or ORMS not tested against PostgreSQL 17.5, and potential need for dump/restore or migration testing.\n- Downtime and connection failures during the upgrade as instances cycle, cluster endpoints fail over, or instances become temporarily unavailable.
>
> **Investigation**
>
> This is not a production-grade risk that requires maintenance planning, compatibility testing (including extensions and parameter expectations), and reliable backups before proceeding.
>
> **✖  Hypothesis disproven**

</details>

---

<h3>🟣 Expected Changes</h3>

<details>
<summary> +/- route53-resource-record-set › auth.overmind-demo.com.</summary>

```diff
--- current
+++ proposed
@@ -2,15 +2,16 @@
 id: github.com/overmindtech/workspace.route53-resource-record-set.auth0_custom_domain_validation_record[0]
 attributes:
-  allow_overwrite: null
-  fqdn: auth.overmind-demo.com
-  id: Z0867153PK7R7O3WA354_auth_CNAME
-  multivalue_answer_routing_policy: false
+  allow_overwrite: (known after apply)
+  fqdn: (known after apply)
+  health_check_id: null
+  id: (known after apply)
+  multivalue_answer_routing_policy: null
   name: auth
-  records:
-    - om-dogfood-cd-0joc0s3ea2qwi7dr.edge.tenants.eu.auth0.com
+  records: (known after apply)
+  set_identifier: null
   terraform_address: aws_route53_record.auth0_custom_domain_validation_record[0]
   terraform_name: auth0_custom_domain_validation_record[0]
   timeouts: null
   ttl: 300
-  type: CNAME
-  zone_id: Z0867153PK7R7O3WA354
+  type: (known after apply)
+  zone_id: (known after apply)

```

</details>
<details>
<summary> +/- route53-resource-record-set › localhost.df.overmind-demo.com.</summary>

```diff
--- current
+++ proposed
@@ -2,11 +2,13 @@
 id: github.com/overmindtech/workspace.route53-resource-record-set.dogfood_cookie_hack[0]
 attributes:
-  allow_overwrite: null
-  fqdn: localhost.df.overmind-demo.com
-  id: Z05283152K3F9BQTAVKDE_localhost.df.overmind-demo.com_A
-  multivalue_answer_routing_policy: false
-  name: localhost.df.overmind-demo.com
+  allow_overwrite: (known after apply)
+  fqdn: (known after apply)
+  health_check_id: null
+  id: (known after apply)
+  multivalue_answer_routing_policy: null
+  name: localhost.df.overmind-prod.com
   records:
     - 127.0.0.1
+  set_identifier: null
   terraform_address: aws_route53_record.dogfood_cookie_hack[0]
   terraform_name: dogfood_cookie_hack[0]

```

</details>
<details>
<summary> +/- route53-resource-record-set › df.overmind-demo.com.</summary>

```diff
--- current
+++ proposed
@@ -2,8 +2,9 @@
 id: github.com/overmindtech/workspace.route53-resource-record-set.dogfood_delegation[0]
 attributes:
-  allow_overwrite: null
-  fqdn: df.overmind-demo.com
-  id: Z0867153PK7R7O3WA354_df_NS
-  multivalue_answer_routing_policy: false
+  allow_overwrite: (known after apply)
+  fqdn: (known after apply)
+  health_check_id: null
+  id: (known after apply)
+  multivalue_answer_routing_policy: null
   name: df
   records:
@@ -12,4 +13,5 @@
     - ns-2017.awsdns-60.co.uk
     - ns-606.awsdns-11.net
+  set_identifier: null
   terraform_address: aws_route53_record.dogfood_delegation[0]
   terraform_name: dogfood_delegation[0]
@@ -17,3 +19,3 @@
   ttl: 300
   type: NS
-  zone_id: Z0867153PK7R7O3WA354
+  zone_id: (known after apply)

```

</details>
<details>
<summary> +/- route53-hosted-zone › /hostedzone/Z0867153PK7R7O3WA354</summary>

```diff
--- current
+++ proposed
@@ -2,17 +2,16 @@
 id: github.com/overmindtech/workspace.route53-hosted-zone.demo_zone[0]
 attributes:
-  arn: arn:aws:route53:::hostedzone/Z0867153PK7R7O3WA354
+  arn: (known after apply)
   comment: Managed by Terraform
+  delegation_set_id: null
   force_destroy: false
-  id: Z0867153PK7R7O3WA354
-  name: overmind-demo.com
-  name_servers:
-    - ns-1437.awsdns-51.org
-    - ns-1608.awsdns-09.co.uk
-    - ns-196.awsdns-24.com
-    - ns-530.awsdns-02.net
-  primary_name_server: ns-196.awsdns-24.com
+  id: (known after apply)
+  name: overmind-prod.com
+  name_servers: (known after apply)
+  primary_name_server: (known after apply)
+  tags: null
+  tags_all: (known after apply)
   terraform_address: aws_route53_zone.demo_zone[0]
   terraform_name: demo_zone[0]
   timeouts: null
-  zone_id: Z0867153PK7R7O3WA354
+  zone_id: (known after apply)

```

</details>
<details>
<summary> ~ cloudfront-distribution › E3K9HBW45QAI7E</summary>

```diff
--- current
+++ proposed
@@ -3,5 +3,5 @@
 attributes:
   aliases:
-    - compliance.overmind-demo.com
+    - compliance.overmind-prod.com
   arn: arn:aws:cloudfront::944651592624:distribution/E3K9HBW45QAI7E
   caller_reference: terraform-20251124110254612000000001
@@ -73,5 +73,5 @@
     - enabled: false
   viewer_certificate:
-    - acm_certificate_arn: arn:aws:acm:us-east-1:944651592624:certificate/3b4d88c9-7963-4076-9d1a-bebe4501747d
+    - acm_certificate_arn: (known after apply)
       cloudfront_default_certificate: false
       minimum_protocol_version: TLSv1.2_2021

```

</details>
<details>
<summary> +/- route53-resource-record-set › compliance.overmind-demo.com.</summary>

```diff
--- current
+++ proposed
@@ -2,11 +2,13 @@
 id: github.com/overmindtech/workspace.route53-resource-record-set.module.drata_proxy_dogfood[0].aws_route53_record.cloudfront[0]
 attributes:
-  allow_overwrite: null
-  fqdn: compliance.overmind-demo.com
-  id: Z0867153PK7R7O3WA354_compliance.overmind-demo.com_CNAME
-  multivalue_answer_routing_policy: false
-  name: compliance.overmind-demo.com
+  allow_overwrite: (known after apply)
+  fqdn: (known after apply)
+  health_check_id: null
+  id: (known after apply)
+  multivalue_answer_routing_policy: null
+  name: compliance.overmind-prod.com
   records:
     - d343qp21z4pleb.cloudfront.net
+  set_identifier: null
   terraform_address: module.drata_proxy_dogfood[0].aws_route53_record.cloudfront[0]
   terraform_name: module.drata_proxy_dogfood[0].aws_route53_record.cloudfront[0]
@@ -14,3 +14,3 @@
   ttl: 300
   type: CNAME
-  zone_id: Z0867153PK7R7O3WA354
+  zone_id: (known after apply)

```

</details>
<details>
<summary> ~ ConfigMap › apiserver-config</summary>

```diff
--- current
+++ proposed
@@ -6,5 +6,5 @@
     API_SERVER_API_KEY_CLIENT_ID: TRYMJyCxFCWU4FycHj1oztPyGyvtiv9f
     API_SERVER_AUTH0_CLIENT_ID: MOr4ISRsSGqXj0PWsDIsRiUtiDh98X1p
-    API_SERVER_CORS_ALLOW_ORIGINS: https://df.overmind-demo.com https://*.df.overmind-demo.com https://localhost.df.overmind-demo.com:*
+    API_SERVER_CORS_ALLOW_ORIGINS: https://df.overmind-demo.com https://*.df.overmind-demo.com https://localhost.df.overmind-prod.com:*
     API_SERVER_CRM_SYNC_INTERVAL: 1m
     API_SERVER_DB_POOL_SIZE: "60"

```

</details>
<details>
<summary> ~ ConfigMap › gateway-config</summary>

```diff
--- current
+++ proposed
@@ -7,5 +7,5 @@
     AUTH0_DOMAIN: auth.overmind-demo.com
     GATEWAY_CLIENT_ID: S7C7SuMzxaEzXjkRKYxy5S72ybs96pcu
-    GATEWAY_CORS_ALLOW_ORIGINS: https://df.overmind-demo.com https://*.df.overmind-demo.com https://localhost.df.overmind-demo.com:*
+    GATEWAY_CORS_ALLOW_ORIGINS: https://df.overmind-demo.com https://*.df.overmind-demo.com https://localhost.df.overmind-prod.com:*
     GATEWAY_DB_POOL_SIZE: "60"
     GATEWAY_OVERMIND_AUTH_URL: https://auth.overmind-demo.com/oauth/token

```

</details>
<details>
<summary> ~ Deployment › api-server</summary>

```diff
--- current
+++ proposed
@@ -45,5 +45,5 @@
                         - name: apiserver-config
                           optional: false
-                  image: ghcr.io/overmindtech/workspace/api-server@sha256:149e83beb3498c597b49a7b5293f5b3159a242d7b8f6557aec81b6419e04f465
+                  image: ghcr.io/overmindtech/workspace/api-server@sha256:ab71d415fd951236f0958fcb53e339b4870afbe69f11a288f5cefe63089debe5
                   image_pull_policy: IfNotPresent
                   liveness_probe:
@@ -115,5 +115,5 @@
                         - name: o11y-secrets
                           optional: false
-                  image: ghcr.io/overmindtech/workspace/api-server@sha256:149e83beb3498c597b49a7b5293f5b3159a242d7b8f6557aec81b6419e04f465
+                  image: ghcr.io/overmindtech/workspace/api-server@sha256:ab71d415fd951236f0958fcb53e339b4870afbe69f11a288f5cefe63089debe5
                   image_pull_policy: IfNotPresent
                   name: generate-nkeys

```

</details>
<details>
<summary> ~ Deployment › frontend</summary>

```diff
--- current
+++ proposed
@@ -86,5 +86,5 @@
                         - name: o11y-secrets
                           optional: false
-                  image: ghcr.io/overmindtech/workspace/frontend-dogfood@sha256:913f7a7822218b189ed16f8c3fdd73a866dcb0fa343b9203cbc327539cba5616
+                  image: ghcr.io/overmindtech/workspace/frontend-dogfood@sha256:b3a269b199378e6e5188ed7fbe6fe9323a8a1db84322f6f710e745c532437494
                   image_pull_policy: IfNotPresent
                   liveness_probe:

```

</details>
<details>
<summary> ~ Deployment › gateway</summary>

```diff
--- current
+++ proposed
@@ -42,5 +42,5 @@
                         - name: gateway-config
                           optional: false
-                  image: ghcr.io/overmindtech/workspace/gateway@sha256:86b354cac2e21f9622016d7114c9ad76a93244c6e9867cfeb00d3e87d4227e33
+                  image: ghcr.io/overmindtech/workspace/gateway@sha256:c7436b2beff06acaf4eb91a8bf66048d88fe950156bbc27ee03882128b5e2d9e
                   image_pull_policy: IfNotPresent
                   liveness_probe:
@@ -100,5 +100,5 @@
                         - name: gateway-config
                           optional: false
-                  image: ghcr.io/overmindtech/workspace/gateway@sha256:86b354cac2e21f9622016d7114c9ad76a93244c6e9867cfeb00d3e87d4227e33
+                  image: ghcr.io/overmindtech/workspace/gateway@sha256:c7436b2beff06acaf4eb91a8bf66048d88fe950156bbc27ee03882128b5e2d9e
                   image_pull_policy: IfNotPresent
                   name: init

```

</details>
<details>
<summary> ~ Deployment › outage-tracker</summary>

```diff
--- current
+++ proposed
@@ -42,5 +42,5 @@
                         - name: outage-tracker-config
                           optional: false
-                  image: ghcr.io/overmindtech/workspace/outage-tracker-service@sha256:dfe6027de463b2c3f16755a7120bec31045b5eff82751038915e4119cfdf2815
+                  image: ghcr.io/overmindtech/workspace/outage-tracker-service@sha256:e03321a2d4895ba59d08bc38569afebac3866b6b9451a12b1a9bca78c2bdf6fa
                   image_pull_policy: IfNotPresent
                   name: outage-tracker

```

</details>
<details>
<summary> ~ Deployment › revlink</summary>

```diff
--- current
+++ proposed
@@ -54,5 +54,5 @@
                         - name: o11y-secrets
                           optional: false
-                  image: ghcr.io/overmindtech/workspace/revlink@sha256:a26d2056dadf722f8e944650cb1e89dd38d07dab01e3fd80a09e2ab0768254c1
+                  image: ghcr.io/overmindtech/workspace/revlink@sha256:c68c0dc6dd8e7fda30f0d62382b4b732bba3cbe80bc02ee15f4760ddbef77727
                   image_pull_policy: IfNotPresent
                   liveness_probe:

```

</details>
<details>
<summary> ~ Secret › apiserver-secrets</summary>

```diff
--- current
+++ proposed
@@ -5,5 +5,5 @@
   binary_data_wo: null
   binary_data_wo_revision: null
-  data: (sensitive value)
+  data: (known after apply)
   data_wo: null
   data_wo_revision: null

```

</details>
<details>
<summary> ~ Secret › frontend-secrets</summary>

```diff
--- current
+++ proposed
@@ -5,5 +5,5 @@
   binary_data_wo: null
   binary_data_wo_revision: null
-  data: (sensitive value)
+  data: (known after apply)
   data_wo: null
   data_wo_revision: null

```

</details>


---

<h3>🟠 Unmapped Changes</h3>

<details>
<summary> +/- auth0_custom_domain › auth0_custom_domain</summary>

```diff
--- current
+++ proposed
@@ -2,21 +2,15 @@
 id: github.com/overmindtech/workspace.auth0_custom_domain.auth0_custom_domain
 attributes:
-  certificate:
-    - certificate_authority: letsencrypt
-      renews_before: "2026-01-25T06:56:03Z"
-      status: provisioned
-  domain: auth.overmind-demo.com
-  id: cd_0jOc0s3ea2QWi7Dr
-  primary: true
-  status: ready
+  certificate: (known after apply)
+  custom_client_ip_header: null
+  domain: auth.overmind-prod.com
+  domain_metadata: null
+  id: (known after apply)
+  origin_domain_name: (known after apply)
+  primary: (known after apply)
+  status: (known after apply)
   terraform_address: auth0_custom_domain.auth0_custom_domain
   terraform_name: auth0_custom_domain
-  tls_policy: recommended
+  tls_policy: (known after apply)
   type: auth0_managed_certs
-  verification:
-    - last_verified_at: "2025-10-27T07:54:13Z"
-      methods:
-        - domain: auth.overmind-demo.com
-          name: CNAME
-          record: om-dogfood-cd-0joc0s3ea2qwi7dr.edge.tenants.eu.auth0.com
-      status: verified
+  verification: (known after apply)

```

</details>
<details>
<summary> +/- auth0_custom_domain_verification › auth0_custom_domain</summary>

```diff
--- current
+++ proposed
@@ -2,7 +2,8 @@
 id: github.com/overmindtech/workspace.auth0_custom_domain_verification.auth0_custom_domain
 attributes:
-  cname_api_key: (sensitive value)
-  custom_domain_id: cd_0jOc0s3ea2QWi7Dr
-  id: cd_0jOc0s3ea2QWi7Dr
+  cname_api_key: (known after apply)
+  custom_domain_id: (known after apply)
+  id: (known after apply)
+  origin_domain_name: (known after apply)
   terraform_address: auth0_custom_domain_verification.auth0_custom_domain
   terraform_name: auth0_custom_domain

```

</details>
<details>
<summary> ~ aws_route53domains_registered_domain › demo_zone[0]</summary>

```diff
--- current
+++ proposed
@@ -30,12 +30,8 @@
   billing_privacy: true
   creation_date: "2022-10-24T14:26:51Z"
-  domain_name: overmind-demo.com
+  domain_name: overmind-prod.com
   expiration_date: "2026-10-24T14:26:51Z"
   id: overmind-demo.com
-  name_server:
-    - name: ns-1437.awsdns-51.org
-    - name: ns-1608.awsdns-09.co.uk
-    - name: ns-196.awsdns-24.com
-    - name: ns-530.awsdns-02.net
+  name_server: (known after apply)
   registrant_contact:
     - address_line_1: 4 Sunlight Square

```

</details>
<details>
<summary> +/- github_actions_organization_secret › op_ro_token_for_gha</summary>

```diff
--- current
+++ proposed
@@ -2,12 +2,13 @@
 id: github.com/overmindtech/workspace.github_actions_organization_secret.op_ro_token_for_gha
 attributes:
-  created_at: 2025-12-01 16:08:25 +0000 UTC
+  created_at: (known after apply)
   destroy_on_drift: false
   encrypted_value: (sensitive value)
-  id: OP_RO_TOKEN
+  id: (known after apply)
   plaintext_value: (sensitive value)
   secret_name: OP_RO_TOKEN
+  selected_repository_ids: null
   terraform_address: github_actions_organization_secret.op_ro_token_for_gha
   terraform_name: op_ro_token_for_gha
-  updated_at: 2025-12-01 16:08:25 +0000 UTC
+  updated_at: (known after apply)
   visibility: private

```

</details>
<details>
<summary> ~ helm_release › gke-dogfood-source[0]</summary>

```diff
--- current
+++ proposed
@@ -18,15 +18,5 @@
   manifest: null
   max_history: 0
-  metadata:
-    - app_version: 0.10.1
-      chart: overmind-kube-source
-      first_deployed: 1.753268988e+09
-      last_deployed: 1.761906897e+09
-      name: gke-dogfood-source
-      namespace: default
-      notes: The overmind source has now been installed ✅
-      revision: 3
-      values: '{"source":{"apiKey":{"value":"ovm_api_nteMz65ZXKYvXbuChZ06zH_qxv77C"},"app":"https://df.overmind-demo.com","clusterName":"dogfood","honeycombApiKey":"C9X9b564tQCMQ0lg1ENPdA","log":"debug","maxParallel":100,"rateLimitBurst":300,"rateLimitQPS":100}}'
-      version: 0.10.1
+  metadata: (known after apply)
   name: gke-dogfood-source
   namespace: default

```

</details>
<details>
<summary> ~ helm_release › k8s-dogfood-source[0]</summary>

```diff
--- current
+++ proposed
@@ -18,15 +18,5 @@
   manifest: null
   max_history: 0
-  metadata:
-    - app_version: 0.10.1
-      chart: overmind-kube-source
-      first_deployed: 1.761053238e+09
-      last_deployed: 1.761906898e+09
-      name: k8s-dogfood-source
-      namespace: default
-      notes: The overmind source has now been installed ✅
-      revision: 3
-      values: '{"source":{"apiKey":{"value":"ovm_api_nteMz65ZXKYvXbuChZ06zH_qxv77C"},"app":"https://df.overmind-demo.com","clusterName":"dogfood","honeycombApiKey":"C9X9b564tQCMQ0lg1ENPdA","log":"debug","maxParallel":100,"rateLimitBurst":300,"rateLimitQPS":100}}'
-      version: 0.10.1
+  metadata: (known after apply)
   name: k8s-dogfood-source
   namespace: default

```

</details>
<details>
<summary> ~ onepassword_item › environment_variables</summary>

```diff
--- current
+++ proposed
@@ -7,5 +7,5 @@
   id: vaults/5rmq6vebgi3c2iejytskh52d6y/items/xkjcynmzjjkwjfenfmkd6mffuy
   note_value: (sensitive value)
-  password: (sensitive value)
+  password: (known after apply)
   port: null
   section:
@@ -90,5 +90,5 @@
           purpose: null
           type: STRING
-          value: (sensitive value)
+          value: (known after apply)
         - id: cd5af2fsxcgm5vfmhc2qlggloq
           label: AUTH0_DOMAIN
@@ -135,5 +135,4 @@
           purpose: null
           type: CONCEALED
-          value: (sensitive value)
         - id: gozzd7wzlrh5itmu4lfz3s5exe
           label: NEXT_PUBLIC_NATS_URL
@@ -150,5 +149,5 @@
           purpose: null
           type: STRING
-          value: (sensitive value)
+          value: (known after apply)
         - id: hm7vpjgueubn4bdnmwqfsencxy
           label: SRCMAN_AUTH0_CLIENT_ID
@@ -200,5 +199,4 @@
           purpose: null
           type: CONCEALED
-          value: (sensitive value)
         - id: w4rnowtqmrcumkdfahx2mhvt5i
           label: API_SERVER_API_KEY_CLIENT_ID

```

</details>
<details>
<summary> +/- vercel_project_environment_variable › cookie_domain</summary>

```diff
--- current
+++ proposed
@@ -2,9 +2,11 @@
 id: github.com/overmindtech/workspace.vercel_project_environment_variable.cookie_domain
 attributes:
+  comment: (known after apply)
+  custom_environment_ids: (known after apply)
   git_branch: null
-  id: 9k4VaiYJVf93qv90
+  id: (known after apply)
   key: AUTH0_COOKIE_DOMAIN
   project_id: prj_pobSoGaGRB9xUIAp3ckLPNRvWMsM
-  sensitive: false
+  sensitive: (known after apply)
   target:
     - development

```

</details>
<details>
<summary> ~ auth0_client › module.centralised.auth0_client.api_keys</summary>

```diff
--- current
+++ proposed
@@ -4,7 +4,7 @@
   app_type: regular_web
   callbacks:
-    - https://*.preview.df.overmind-demo.com/api/keys/callback
+    - https://*.preview.df.overmind-prod.com/api/keys/callback
     - https://api.df.overmind-demo.com/api/keys/callback
-    - https://localhost.df.overmind-demo.com:8000/api/keys/callback
+    - https://localhost.df.overmind-prod.com:8000/api/keys/callback
   client_id: TRYMJyCxFCWU4FycHj1oztPyGyvtiv9f
   cross_origin_auth: false

```

</details>
<details>
<summary> ~ auth0_client › module.centralised.auth0_client.frontend</summary>

```diff
--- current
+++ proposed
@@ -3,14 +3,14 @@
 attributes:
   allowed_logout_urls:
-    - https://*.preview.df.overmind-demo.com
+    - https://*.preview.df.overmind-prod.com
     - https://df.overmind-demo.com
-    - https://localhost.df.overmind-demo.com:3000
-    - https://localhost.df.overmind-demo.com:8000
+    - https://localhost.df.overmind-prod.com:3000
+    - https://localhost.df.overmind-prod.com:8000
   app_type: regular_web
   callbacks:
-    - https://*.preview.df.overmind-demo.com/auth/callback
+    - https://*.preview.df.overmind-prod.com/auth/callback
     - https://df.overmind-demo.com/auth/callback
-    - https://localhost.df.overmind-demo.com:3000/auth/callback
-    - https://localhost.df.overmind-demo.com:8000/auth/callback
+    - https://localhost.df.overmind-prod.com:3000/auth/callback
+    - https://localhost.df.overmind-prod.com:8000/auth/callback
   client_id: nxt9CsXX72tgqiLbq6CFL8LKTPWCImBL
   cross_origin_auth: false

```

</details>
<details>
<summary> +/- aws_acm_certificate › module.drata_proxy_dogfood[0].aws_acm_certificate.drata_proxy</summary>

```diff
--- current
+++ proposed
@@ -2,25 +2,27 @@
 id: github.com/overmindtech/workspace.aws_acm_certificate.module.drata_proxy_dogfood[0].aws_acm_certificate.drata_proxy
 attributes:
-  arn: arn:aws:acm:us-east-1:944651592624:certificate/3b4d88c9-7963-4076-9d1a-bebe4501747d
+  arn: (known after apply)
+  certificate_authority_arn: null
   certificate_body: null
   certificate_chain: null
-  domain_name: compliance.overmind-demo.com
+  domain_name: compliance.overmind-prod.com
   domain_validation_options:
-    - domain_name: compliance.overmind-demo.com
-      resource_record_name: _ee05637aaad45106328982c9e29bb7fc.compliance.overmind-demo.com.
-      resource_record_type: CNAME
-      resource_record_value: _76dbfef515f9eecc0af67a71858cf33b.jkddzztszm.acm-validations.aws.
-  id: arn:aws:acm:us-east-1:944651592624:certificate/3b4d88c9-7963-4076-9d1a-bebe4501747d
-  key_algorithm: RSA_2048
-  not_after: "2026-12-23T23:59:59Z"
-  not_before: "2025-11-24T00:00:00Z"
-  options:
-    - certificate_transparency_logging_preference: ENABLED
-  pending_renewal: false
+    - domain_name: compliance.overmind-prod.com
+      resource_record_name: (known after apply)
+      resource_record_type: (known after apply)
+      resource_record_value: (known after apply)
+  early_renewal_duration: null
+  id: (known after apply)
+  key_algorithm: (known after apply)
+  not_after: (known after apply)
+  not_before: (known after apply)
+  options: (known after apply)
+  pending_renewal: (known after apply)
   private_key: (sensitive value)
-  renewal_eligibility: ELIGIBLE
-  status: ISSUED
+  renewal_eligibility: (known after apply)
+  renewal_summary: (known after apply)
+  status: (known after apply)
   subject_alternative_names:
-    - compliance.overmind-demo.com
+    - compliance.overmind-prod.com
   tags:
     Environment: dogfood
@@ -31,4 +28,5 @@
   terraform_address: module.drata_proxy_dogfood[0].aws_acm_certificate.drata_proxy
   terraform_name: module.drata_proxy_dogfood[0].aws_acm_certificate.drata_proxy
-  type: AMAZON_ISSUED
+  type: (known after apply)
+  validation_emails: (known after apply)
   validation_method: DNS

```

</details>
<details>
<summary> +/- aws_acm_certificate_validation › module.drata_proxy_dogfood[0].aws_acm_certificate_validation.drata_proxy</summary>

```diff
--- current
+++ proposed
@@ -2,10 +2,9 @@
 id: github.com/overmindtech/workspace.aws_acm_certificate_validation.module.drata_proxy_dogfood[0].aws_acm_certificate_validation.drata_proxy
 attributes:
-  certificate_arn: arn:aws:acm:us-east-1:944651592624:certificate/3b4d88c9-7963-4076-9d1a-bebe4501747d
-  id: 2025-11-24 09:25:04.783 +0000 UTC
+  certificate_arn: (known after apply)
+  id: (known after apply)
   terraform_address: module.drata_proxy_dogfood[0].aws_acm_certificate_validation.drata_proxy
   terraform_name: module.drata_proxy_dogfood[0].aws_acm_certificate_validation.drata_proxy
   timeouts:
     create: 5m
-  validation_record_fqdns:
-    - _ee05637aaad45106328982c9e29bb7fc.compliance.overmind-demo.com
+  validation_record_fqdns: (known after apply)

```

</details>
<details>
<summary> - route53-resource-record-set › module.drata_proxy_dogfood[0].aws_route53_record.acm_validation["compliance.overmind-demo.com"]</summary>

```diff
--- current
+++ proposed
@@ -1,16 +0,0 @@
-type: route53-resource-record-set
-id: github.com/overmindtech/workspace.route53-resource-record-set.module.drata_proxy_dogfood[0].aws_route53_record.acm_validation["compliance.overmind-demo.com"]
-attributes:
-  allow_overwrite: true
-  fqdn: _ee05637aaad45106328982c9e29bb7fc.compliance.overmind-demo.com
-  id: Z0867153PK7R7O3WA354__ee05637aaad45106328982c9e29bb7fc.compliance.overmind-demo.com._CNAME
-  multivalue_answer_routing_policy: false
-  name: _ee05637aaad45106328982c9e29bb7fc.compliance.overmind-demo.com
-  records:
-    - _76dbfef515f9eecc0af67a71858cf33b.jkddzztszm.acm-validations.aws.
-  terraform_address: module.drata_proxy_dogfood[0].aws_route53_record.acm_validation["compliance.overmind-demo.com"]
-  terraform_name: module.drata_proxy_dogfood[0].aws_route53_record.acm_validation["compliance.overmind-demo.com"]
-  timeouts: null
-  ttl: 300
-  type: CNAME
-  zone_id: Z0867153PK7R7O3WA354

```

</details>
<details>
<summary> + aws_route53_record › module.drata_proxy_dogfood[0].aws_route53_record.acm_validation["compliance.overmind-prod.com"]</summary>

```diff
--- current
+++ proposed
@@ -0,0 +1,17 @@
+type: aws_route53_record
+id: github.com/overmindtech/workspace.aws_route53_record.module.drata_proxy_dogfood[0].aws_route53_record.acm_validation["compliance.overmind-prod.com"]
+attributes:
+  allow_overwrite: true
+  fqdn: (known after apply)
+  health_check_id: null
+  id: (known after apply)
+  multivalue_answer_routing_policy: null
+  name: (known after apply)
+  records: (known after apply)
+  set_identifier: null
+  terraform_address: module.drata_proxy_dogfood[0].aws_route53_record.acm_validation["compliance.overmind-prod.com"]
+  terraform_name: module.drata_proxy_dogfood[0].aws_route53_record.acm_validation["compliance.overmind-prod.com"]
+  timeouts: null
+  ttl: 300
+  type: (known after apply)
+  zone_id: (known after apply)

```

</details>
<details>
<summary> ~ helm_release › module.services.helm_release.nats</summary>

```diff
--- current
+++ proposed
@@ -18,14 +18,5 @@
   manifest: null
   max_history: 0
-  metadata:
-    - app_version: 2.12.2
-      chart: nats
-      first_deployed: 1.761045426e+09
-      last_deployed: 1.763729278e+09
-      name: nats
-      namespace: default
-      revision: 4
-      values: '{"config":{"merge":{"operator":"(sensitive value)","resolver_preload":{"AC4MDWG4YTZR2SLDX4YQDOUY3JXTTMIHJUIZT53HAPJRGY7KYZOHDTMG":"(sensitive value)"},"system_account":"AC4MDWG4YTZR2SLDX4YQDOUY3JXTTMIHJUIZT53HAPJRGY7KYZOHDTMG"},"monitor":{"enabled":true},"resolver":{"enabled":true,"merge":{"allow_delete":true,"interval":"2m","timeout":"500ms","type":"full"},"pvc":{"storageClassName":"api-server-efs-sc"}},"websocket":{"enabled":true,"ingress":{"enabled":false}}},"service":{"merge":{"metadata":{"annotations":{"alb.ingress.kubernetes.io/healthcheck-port":"8222"}}},"ports":{"monitor":{"enabled":true}}}}'
-      version: 2.12.2
+  metadata: (known after apply)
   name: nats
   namespace: default

```

</details>
<details>
<summary> ~ kubectl_manifest › module.services.module.srcman_eks.kubectl_manifest.srcman_controller["/apis/apps/v1/namespaces/srcman-system/deployments/srcman-controller-manager-v4"]</summary>

```diff
--- current
+++ proposed
@@ -61,5 +61,5 @@
                 name: tracing-keys
                 optional: false
-            image: ghcr.io/overmindtech/workspace/srcman@sha256:1d69b11e9691e96fa57642ef670eeb3205dd3f1c50a527503645b7630ef9ba02
+            image: ghcr.io/overmindtech/workspace/srcman@sha256:28675102279464aa69b663ede24de8db3dc3260f0a89faeb60b53188b70ec7c7
             liveness... (truncated)
   yaml_incluster: (sensitive value)

```

</details>
<details>
<summary> ~ kubectl_manifest › module.services.module.srcman_gke.kubectl_manifest.srcman_controller["/apis/apps/v1/namespaces/srcman-system/deployments/srcman-controller-manager-v4"]</summary>

```diff
--- current
+++ proposed
@@ -61,5 +61,5 @@
                 name: tracing-keys
                 optional: false
-            image: ghcr.io/overmindtech/workspace/srcman@sha256:1d69b11e9691e96fa57642ef670eeb3205dd3f1c50a527503645b7630ef9ba02
+            image: ghcr.io/overmindtech/workspace/srcman@sha256:28675102279464aa69b663ede24de8db3dc3260f0a89faeb60b53188b70ec7c7
             liveness... (truncated)
   yaml_incluster: (sensitive value)

```

</details>



---

<h3>💥 Blast Radius</h3>

**Items** ` 122 `

**Edges** ` 551 `
<!-- Sticky Pull Request Commentchange -->

# Flexo MMS Railway

This directory contains sample files for deploying the Flexo MMS service set on
Railway.

Railway does not run `docker-compose.yml` as one deployment unit. Each Compose
service should be created as a Railway service in the same project environment,
and services should talk over Railway private networking with
`SERVICE_NAME.railway.internal`.

## Services

Create these services in one Railway project:

| Service | Source | Dockerfile path | Public domain |
| --- | --- | --- | --- |
| `openldap-server` | GitHub repo | `railway/openldap-server/Dockerfile` | No |
| `quad-store-server` | GitHub repo | `railway/quad-store-server/Dockerfile` | No |
| `minio-server` | GitHub repo | `railway/minio-server/Dockerfile` | No |
| `auth-service` | GitHub repo | `railway/auth-service/Dockerfile` | Yes, for `/login` |
| `store-service` | GitHub repo | `railway/store-service/Dockerfile` | No |
| `layer1-service` | GitHub repo | `railway/layer1-service/Dockerfile` | Yes, for the API |

The `quad-store-server` service includes a copy of
`docker-compose/mount/cluster.trig` at `railway/quad-store-server/cluster.trig`.
Railway deploys each service from its own Docker build context, so it cannot use
the Docker Compose bind mount directly. Keep this copy synchronized with the
Docker Compose seed file.

## Variables

Paste the following files into each service's Railway Variables raw editor:

| Service | Variable files |
| --- | --- |
| `openldap-server` | `railway/env/flexo-mms-openldap.env` |
| `quad-store-server` | `railway/env/flexo-mms-quad-store.env` |
| `minio-server` | `railway/env/flexo-mms-minio.env` |
| `auth-service` | `railway/env/flexo-mms-jwt.env`, `railway/env/flexo-mms-auth.env` |
| `store-service` | `railway/env/flexo-mms-jwt.env`, `railway/env/flexo-mms-store.env` |
| `layer1-service` | `railway/env/flexo-mms-jwt.env`, `railway/env/flexo-mms-layer1.env` |

Before deploying beyond a disposable test environment, replace:

- `JWT_SECRET`
- `MINIO_ROOT_USER`
- `MINIO_ROOT_PASSWORD`
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- LDAP demo user passwords

The sample Railway variables keep `FLEXO_MMS_ROOT_CONTEXT` and
`LDAP_GROUP_STORE_CONTEXT` aligned with the seeded access-control data in
`cluster.trig`. If you change the root context to a public Railway domain,
regenerate or update `cluster.trig` so the seeded policies use the same root.

## Volumes

Attach Railway volumes before relying on data persistence:

| Service | Mount path |
| --- | --- |
| `minio-server` | `/data` |
| `quad-store-server` | `/fuseki` |

The LDAP service is configured as a demo service and is not persisted here.

## Deployment notes

1. Create an empty Railway project.
2. Add the six services above from this GitHub repository.
3. For each service, set the Dockerfile path shown in the table.
4. Paste the matching `railway/env/flexo-mms-*.env` variables.
5. Add volumes for `minio-server` and `quad-store-server`.
6. Generate public Railway domains only for `auth-service` and
   `layer1-service`.
7. Deploy backing services first, then `auth-service`, `store-service`, and
   finally `layer1-service`.

Railway has no direct `depends_on` equivalent, so service startup should be
validated through deploy logs. If a service starts before one of its
dependencies is ready, redeploy that service after the dependency is healthy.

After deployment:

```sh
curl -u user01:password1 https://<auth-service-domain>/login
curl https://<layer1-service-domain>/
```

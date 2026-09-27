# Supreme ERP

## Default administrator account

When the backend starts with an empty `user` table, it creates this initial administrator account:

- Email: `admin@email.com`
- Password: `admin`

Change these credentials before using the application outside local development.

## Run with Docker Compose

From the repository root, build and start all services:

```bash
docker compose up -d --build
```

The applications are available at:

- ERP frontend: <http://localhost:3000>
- Storefront: <http://localhost:3001>
- Accounting: <http://localhost:3002>
- Backend API: <http://localhost:5000>

To view service logs:

```bash
docker compose logs -f
```

To stop the services:

```bash
docker compose down
```

## Seed the database

After MySQL and the backend are running, load the demo data with:

```bash
docker compose run --rm backend npm run db:seed
```

`backend` is the Docker Compose service name. This starts a temporary backend container, runs the seed files, and removes the temporary container afterward.

## Local Midtrans payment testing

Local Docker services are not exposed through a public HTTPS URL, so Midtrans cannot deliver its payment webhook to this backend. For local development, set the following flag in `supreme-erp-be/.env`:

```env
SKIP_MIDTRANS_PAYMENT_VERIFICATON=true
```

With this flag enabled, the customer still completes payment in Midtrans Snap. After Snap reports success, the Storefront confirms the order directly with the local backend instead of waiting for a Midtrans webhook. Do not enable this option in production.

Use these Sandbox card numbers when testing card payment:

| Success cards | Decline cards |
| --- | --- |
| Visa: `4811 1111 1111 1114` | Visa: `4811 1111 1111 1114` |
| Mastercard: `5211 1111 1111 1117` | Mastercard: `5111 1111 1111 1118` |

EXP Date: 12/28
CVV: 123

For setting up n8n you need to copy this file into the n8n docker container:

docker cp supreme-erp-n8n/workflows/daily-finance-digest.json \
  supreme-erp-n8n-1:/tmp/daily-finance-digest.json

docker exec supreme-erp-n8n-1 \
  n8n import:workflow --input=/tmp/daily-finance-digest.json

======

docker cp supreme-erp-n8n/workflows/get-finance-digest.json \
  supreme-erp-n8n-1:/tmp/get-finance-digest.json

docker exec supreme-erp-n8n-1 \
  n8n import:workflow --input=/tmp/get-finance-digest.json
# Catalog API Assignment

## Task 1: Memory-Based Catalog API + Dockerization

### Objective

Create a simple REST API in any programming language or framework of your choice that manages a **memory-based product catalog**.

The catalog data can be stored in memory using a list, map, dictionary, or any similar in-memory data structure.

---

## Functional Requirements

The API should support the following operations.

### 1. Add a New Item to Catalog

Create an endpoint to add a new product/item to the catalog.

**HTTP Method:** `POST`

**Endpoint:**

```http
POST /products
```

**Sample Request Body:**

```json
{
  "id": "1",
  "name": "Laptop",
  "price": 55000,
  "category": "Electronics"
}
```

**Sample Response:**

```json
{
  "message": "Product added successfully",
  "product": {
    "id": "1",
    "name": "Laptop",
    "price": 55000,
    "category": "Electronics"
  }
}
```

---

### 2. Remove an Existing Item from Catalog

Create an endpoint to remove a product from the catalog using its ID.

**HTTP Method:** `DELETE`

**Endpoint:**

```http
DELETE /products/{id}
```

**Sample Response:**

```json
{
  "message": "Product removed successfully"
}
```

---

### 3. Get All Products from Catalog

Create an endpoint to fetch all products available in the catalog.

**HTTP Method:** `GET`

**Endpoint:**

```http
GET /products
```

**Sample Response:**

```json
[
  {
    "id": "1",
    "name": "Laptop",
    "price": 55000,
    "category": "Electronics"
  }
]
```

---

## Technical Requirements

The application should:

1. Use appropriate HTTP methods.
2. Store catalog data in memory.
3. Have a proper request and response structure.
4. Handle basic error cases, such as:
   - Product not found while deleting.
   - Duplicate product ID while adding.
   - Invalid request body.
5. Expose the API on a configurable port.

---

## Docker Requirements

Dockerize the application so that it can run inside a container.

You need to:

1. Create a `Dockerfile`.
2. Build the Docker image locally.
3. Run the container and verify the APIs.
4. Push the Docker image to Docker Hub.

**Example Commands:**

```bash
docker build -t <dockerhub-username>/catalog-api:latest .
```

```bash
docker run -p 8080:8080 <dockerhub-username>/catalog-api:latest
```

```bash
docker push <dockerhub-username>/catalog-api:latest
```

---

## Expected Deliverables for Task 1

Submit the following:

1. Source code of the API.
2. `Dockerfile`.
3. Docker Hub image URL.
4. API testing screenshots or curl commands.
5. Short `README.md` explaining how to run the application.

---

# Task 2: Stateful Catalog with PostgreSQL and Container Storage

## Objective

Extend the catalog application to store product data in a database instead of memory.

Use **PostgreSQL** as the database and configure container storage so that the data persists even if the containers are destroyed and recreated.

---

## Functional Requirements

The API should support the same catalog operations.

### 1. Add a Product

```http
POST /products
```

### 2. Remove a Product

```http
DELETE /products/{id}
```

### 3. Get All Products

```http
GET /products
```

---

## Database Requirements

Use PostgreSQL to store product data.

Create a table similar to the following:

```sql
CREATE TABLE products (
    id VARCHAR(50) PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    category VARCHAR(255)
);
```

---

## Container Storage Requirement

Use container storage so that PostgreSQL data remains available even after containers are removed.

You can use either:

- Docker volume
- Bind mount

### Example Using Docker Volume

Create a Docker volume:

```bash
docker volume create postgres_catalog_data
```

Run PostgreSQL with persistent volume:

```bash
docker run --name catalog-postgres \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=admin \
  -e POSTGRES_DB=catalogdb \
  -p 5432:5432 \
  -v postgres_catalog_data:/var/lib/postgresql/data \
  postgres:latest
```

---

## Persistence Verification

You need to verify that the data persists after container destruction.

### Steps to Verify

1. Start the PostgreSQL container with a volume.
2. Start the catalog API container.
3. Add some products using the API.
4. Stop and remove the PostgreSQL container.

```bash
docker stop catalog-postgres
docker rm catalog-postgres
```

5. Recreate the PostgreSQL container using the same volume.

```bash
docker run --name catalog-postgres \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=admin \
  -e POSTGRES_DB=catalogdb \
  -p 5432:5432 \
  -v postgres_catalog_data:/var/lib/postgresql/data \
  postgres:latest
```

6. Start the API again.
7. Call the following endpoint:

```http
GET /products
```

8. Verify that the previously added products are still available.

---

## Optional: Docker Compose

You can also use `docker-compose.yml` to run both the API and PostgreSQL together.

```yaml
services:
  app:
    image: <dockerhub-username>/catalog-api:latest
    ports:
      - "8080:8080"
    environment:
      DB_HOST: postgres
      DB_PORT: 5432
      DB_NAME: catalogdb
      DB_USER: admin
      DB_PASSWORD: admin
    depends_on:
      - postgres

  postgres:
    image: postgres:latest
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin
      POSTGRES_DB: catalogdb
    volumes:
      - postgres_catalog_data:/var/lib/postgresql/data

volumes:
  postgres_catalog_data:
```

---

## Expected Deliverables for Task 2

Submit the following:

1. Updated API source code with PostgreSQL integration.
2. Database schema or migration script.
3. `Dockerfile`.
4. `docker-compose.yml` or Docker run commands.
5. Docker Hub image URL.
6. Steps proving persistence after container deletion and recreation.
7. Screenshots or curl commands showing:
   - Product creation.
   - Fetching products.
   - Container deletion.
   - Container recreation.
   - Data still available after restart.

---

# Final Submission Checklist

## Task 1 Checklist

- [ ] Memory-based catalog API created.
- [ ] `POST /products` implemented.
- [ ] `DELETE /products/{id}` implemented.
- [ ] `GET /products` implemented.
- [ ] Basic error handling added.
- [ ] `Dockerfile` created.
- [ ] Docker image built locally.
- [ ] Docker image pushed to Docker Hub.
- [ ] `README.md` added.

## Task 2 Checklist

- [ ] PostgreSQL integrated.
- [ ] Product data stored in the database.
- [ ] Persistent Docker volume configured.
- [ ] Data survives container deletion and recreation.
- [ ] Docker Compose file or Docker run commands provided.
- [ ] Persistence verification documented.
- [ ] API testing screenshots or curl commands added.
- [ ] `README.md` updated.

# 🚀 OpenShift Podman Lab: MySQL & PostgreSQL with Persistent Storage

This lab demonstrates how to use **Podman** to run **MySQL** and **PostgreSQL** containers with **host-mounted persistent storage**, ensuring data survives even after the container is removed.

---

## 📘 Task 1: Running MySQL Container with Persistent Storage

### 🔹 Subtask 1.1: Create Persistent Volume

```bash
mkdir -p mysql-data
```

📌 *Explanation*: This directory will be mounted into the container and used to persist MySQL data outside the container.

---

### 🔹 Subtask 1.2: Launch MySQL Container

```bash
podman run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=redhat123 \
  -e MYSQL_DATABASE=testdb \
  -e MYSQL_USER=testuser \
  -e MYSQL_PASSWORD=user123 \
  -v $(pwd)/mysql-data:/var/lib/mysql \
  -p 3306:3306 \
  docker.io/library/mysql:8.0
```

📌 **Key Notes**:
- `-v $(pwd)/mysql-data:/var/lib/mysql`: Mounts local volume for persistence
- Environment variables create initial DB, user, and password

---

### 🔹 Subtask 1.3: Test Database Connection

```bash
podman exec -it mysql-db mysql -u testuser -puser123 testdb
```

Once inside the MySQL shell:

```sql
CREATE TABLE lab_data (
    id INT AUTO_INCREMENT PRIMARY KEY,
    message VARCHAR(255)
);

INSERT INTO lab_data (message) VALUES ('Persistent test data');

SELECT * FROM lab_data;

exit;
```

✅ *Expected Output*: You should see the inserted record returned by `SELECT`.

---

## 📘 Task 2: Verify Data Persistence

### 🔹 Subtask 2.1: Remove the Container

```bash
podman stop mysql-db
podman rm mysql-db
```

---

### 🔹 Subtask 2.2: Recreate the Container with Same Volume

```bash
podman run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=redhat123 \
  -v $(pwd)/mysql-data:/var/lib/mysql \
  -p 3306:3306 \
  docker.io/library/mysql:8.0
```

---

### 🔹 Subtask 2.3: Verify Data Survival

```bash
podman exec -it mysql-db mysql -u testuser -puser123 testdb -e "SELECT * FROM lab_data;"
```

✅ *Expected Output*: The previously inserted record should still be present, confirming persistent storage is working correctly.

---

## 🧪 Task 3: PostgreSQL Implementation (Optional Challenge)

### 🔹 Subtask 3.1: Create PostgreSQL Volume

```bash
mkdir -p pg-data
```

---

### 🔹 Subtask 3.2: Launch PostgreSQL Container

```bash
podman run -d \
  --name postgres-db \
  -e POSTGRES_PASSWORD=redhat123 \
  -e POSTGRES_USER=testuser \
  -e POSTGRES_DB=testdb \
  -v $(pwd)/pg-data:/var/lib/postgresql/data \
  -p 5432:5432 \
  docker.io/library/postgres:13
```

---

### 🔹 Subtask 3.3: Test Data Persistence

```bash
podman exec -it postgres-db psql -U testuser -d testdb -c "
CREATE TABLE lab_data (
    id SERIAL PRIMARY KEY,
    message TEXT
);

INSERT INTO lab_data (message) VALUES ('Postgres persistent data');

SELECT * FROM lab_data;"
```

✅ *Expected Output*: Output should display the inserted data.

---

## ✅ Summary

| Task        | Goal                                       | Outcome                     |
|-------------|--------------------------------------------|-----------------------------|
| MySQL Setup | Create container with persistent storage   | Container runs & persists   |
| Data Test   | Insert & verify table data                 | Data inserted successfully  |
| Persistence | Remove/recreate container & check data     | Data survives               |
| PostgreSQL  | Optional: Setup & test persistence         | Works similarly             |

---

## 📌 Notes

- Make sure Podman is installed and running correctly.
- Use `podman ps -a` to view all containers.
- Use `podman logs <container_name>` to debug any issues.

---

## 🛠 Author

**Abdullha Saleem** – DevOps | Linux | Docker | Podman | Kubernetes | AIOps

📫 *Reach me on LinkedIn, GitHub, or your favorite cloud terminal!*

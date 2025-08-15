# JSP E‑Commerce

> Production‑ready README for a JSP + Servlets + JDBC e‑commerce project. Replace placeholders like **`<YOUR_VALUE>`**.

![Java](https://img.shields.io/badge/Java-17+-red)
![JSP](https://img.shields.io/badge/JSP-2.3-blue)
![Servlets](https://img.shields.io/badge/Servlets-4.0-blueviolet)
![Tomcat](https://img.shields.io/badge/Tomcat-9%2F10-orange)
![Build](https://img.shields.io/badge/Build-Maven%20or%20Gradle-brightgreen)
![License](https://img.shields.io/badge/License-MIT-informational)

---

## 📌 Overview

A full‑stack e‑commerce web application built using **JSP, Servlets, JDBC**, and **MySQL/PostgreSQL**, deployed on **Apache Tomcat**. It includes user authentication, product browsing with filters and search, cart & checkout with shipping, and an admin dashboard for product/order management.

**Live Demo:** `<add_url_if_any>`
**Project Board / Issues:** `<link_if_any>`

## ✨ Features

* 👤 **Auth:** User Registration & Login (session management)
* 🏷️ **Catalog:** Products grouped by categories, with **search**, **filters**, and **sorting**
* 🛒 **Cart:** Add/Remove/Update items, dynamic cart counter
* 💳 **Checkout:** Shipping form + payment step (mock or gateway‑ready)
* 📦 **Orders:** Place order, view order history
* 🛠️ **Admin:** Manage products, categories, and orders
* 🖥️ **UI:** Responsive pages; header with profile popover & logout

## 🧰 Tech Stack

* **Frontend:** JSP, JSTL, HTML5, CSS3, JavaScript (AJAX)
* **Backend:** Java 17+, Servlets, JDBC
* **Server:** Apache Tomcat 9/10
* **Database:** MySQL *or* PostgreSQL
* **Build/IDE:** Maven/Gradle; NetBeans/IntelliJ/Eclipse

## 🗂️ Project Structure

```
src/
  main/
    java/
      com/example/app/
        controllers/        # Servlets
        dao/                # Data access objects
        models/             # POJOs
        utils/              # DB utils, validators
    resources/
      application.properties
    webapp/
      WEB-INF/
        web.xml
        views/              # JSPs
      assets/               # css, js, images
pom.xml | build.gradle
README.md
```

## 🔐 Environment Configuration

Create an `application.properties` (or use context params in `web.xml`):

```
app.env=prod
jdbc.url=jdbc:mysql://<host>:<port>/<db>
jdbc.username=<user>
jdbc.password=<password>
jdbc.driver=com.mysql.cj.jdbc.Driver
# For PostgreSQL:
# jdbc.url=jdbc:postgresql://<host>:<port>/<db>
# jdbc.driver=org.postgresql.Driver

# Optional
app.secret=<random_secure_string>
session.timeout.minutes=30
```

If using JNDI (recommended for Tomcat), configure in `conf/context.xml`:

```xml
<Resource name="jdbc/AppDS" auth="Container"
          type="javax.sql.DataSource" maxTotal="50" maxIdle="10" maxWaitMillis="10000"
          username="<user>" password="<password>"
          driverClassName="com.mysql.cj.jdbc.Driver"
          url="jdbc:mysql://<host>:<port>/<db>?useSSL=false&serverTimezone=UTC"/>
```

Then in `web.xml`:

```xml
<resource-ref>
  <description>DB Connection</description>
  <res-ref-name>jdbc/AppDS</res-ref-name>
  <res-type>javax.sql.DataSource</res-type>
  <res-auth>Container</res-auth>
</resource-ref>
```

## 🧪 Sample Database Schema (MySQL)

```sql
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(80) NOT NULL,
  email VARCHAR(120) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  role ENUM('USER','ADMIN') DEFAULT 'USER',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE categories (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(80) UNIQUE NOT NULL
);

CREATE TABLE products (
  id INT PRIMARY KEY AUTO_INCREMENT,
  category_id INT NOT NULL,
  title VARCHAR(120) NOT NULL,
  description TEXT,
  price DECIMAL(10,2) NOT NULL,
  image_url VARCHAR(255),
  stock INT DEFAULT 0,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (category_id) REFERENCES categories(id)
);

CREATE TABLE carts (
  id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE TABLE cart_items (
  id INT PRIMARY KEY AUTO_INCREMENT,
  cart_id INT NOT NULL,
  product_id INT NOT NULL,
  quantity INT NOT NULL,
  unit_price DECIMAL(10,2) NOT NULL,
  FOREIGN KEY (cart_id) REFERENCES carts(id),
  FOREIGN KEY (product_id) REFERENCES products(id)
);

CREATE TABLE orders (
  id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT NOT NULL,
  total DECIMAL(10,2) NOT NULL,
  status VARCHAR(30) DEFAULT 'PLACED',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE TABLE order_items (
  id INT PRIMARY KEY AUTO_INCREMENT,
  order_id INT NOT NULL,
  product_id INT NOT NULL,
  quantity INT NOT NULL,
  price DECIMAL(10,2) NOT NULL,
  FOREIGN KEY (order_id) REFERENCES orders(id),
  FOREIGN KEY (product_id) REFERENCES products(id)
);
```

### Seed Data

```sql
INSERT INTO categories(name) VALUES ('Electronics'), ('Books'), ('Fashion');
INSERT INTO products(category_id, title, description, price, stock) VALUES
 (1, 'Headphones', 'Wireless over‑ear', 1999.00, 25),
 (2, 'DSA Handbook', 'Algorithms & practice', 499.00, 100),
 (3, 'T‑Shirt', '100% cotton', 299.00, 50);
```

## ▶️ Getting Started

### Prerequisites

* Java 17+
* Maven 3.9+ (or Gradle 8+)
* Apache Tomcat 9/10
* MySQL 8+ or PostgreSQL 14+

### Build (Maven)

```bash
mvn clean package
```

The output `*.war` will be in `target/`. Deploy it to Tomcat’s `webapps/` directory or via Tomcat Manager.

### Run (Tomcat)

* Copy `target/app.war` to `<TOMCAT_HOME>/webapps/app.war`
* Start Tomcat and open: `http://localhost:8080/app`

## 🌐 Deployment

* **AWS Elastic Beanstalk (Tomcat platform)** – upload the WAR
* **Render / Railway / DigitalOcean / VPS** – install Tomcat + JDK; deploy WAR; configure env/JNDI
* **Docker** – build an image bundling Tomcat + WAR

**Dockerfile (example):**

```dockerfile
FROM tomcat:10.1-jdk17
COPY target/app.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
CMD ["catalina.sh", "run"]
```

## 🔀 Routing & Key Endpoints (examples)

* `GET /` – Home / product list
* `GET /product?id=123` – Product details
* `POST /cart/add` – Add to cart `{productId, qty}`
* `POST /checkout` – Create order
* `POST /auth/login`, `POST /auth/register`, `POST /auth/logout`
* `GET /admin/products` (ADMIN)

## 🔒 Security Notes

* Store **hashed passwords** (BCrypt/Argon2)
* Use **PreparedStatements** to prevent SQL injection
* Set `HttpOnly`, `Secure`, `SameSite=Lax` for session cookies
* CSRF token for state‑changing POSTs

## 🧹 Code Quality

* Java conventions, meaningful package structure
* DAO layer returning models; no SQL in JSPs
* JSPs only for rendering; logic in Servlets/Services

## 🧰 Scripts (optional)

Add `Makefile` or shell scripts for repetitive tasks (build, run, seed, test).

## 🧭 Roadmap

* [ ] Pagination and caching for product list
* [ ] Payment gateway integration (Razorpay/Stripe)
* [ ] Image upload & CDN
* [ ] Email notifications
* [ ] Unit & integration tests

## 🤝 Contributing

1. Fork the repo
2. Create a feature branch: `git checkout -b feat/<name>`
3. Commit: `git commit -m "feat: <message>"`
4. Push & open a PR

## 🐞 Troubleshooting

* **HTTP 500 / DB errors:** verify JNDI or JDBC URL & driver jar
* **JSP not rendering:** check view path and `web.xml` mappings
* **Session issues:** confirm cookie domain/path and timeout

## 📸 Screenshots

> Create a `/screenshots/` folder and add images, then reference below.

| Page     | Screenshot                   |
| -------- | ---------------------------- |
| Home     | `./screenshots/home.png`     |
| Product  | `./screenshots/product.png`  |
| Cart     | `./screenshots/cart.png`     |
| Checkout | `./screenshots/checkout.png` |
| Admin    | `./screenshots/admin.png`    |

## 📄 License

This project is licensed under the **MIT License** — see `LICENSE` for details.

## 🙌 Acknowledgements

* JSP & Servlet docs, JSTL
* Tomcat docs, JDBC drivers
* Icons/images from `<source_attributions>`

---

### 📝 How to Customize Quickly

* Replace placeholders `<...>` and update badges/versions
* Update schema/DAO names to match your code
* Add your **project name**, **logo**, and **demo link**
* Paste real endpoint paths and parameters
* Add your screenshots and GIFs

> Author: **Siddharth Darkunde**
> Contact: `<email or LinkedIn>`

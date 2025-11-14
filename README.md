# Spring Boot JWT Backend with Single Page Frontend

## Overview

This project demonstrates a simple JWT-based authentication system implemented with **Spring Boot** on the backend and a **single-page frontend** (vanilla HTML + JavaScript) to interact with it. The frontend handles login, token storage, and accessing a protected endpoint.

---

## Backend Structure and Roles of Each Class

### 1. **SecurityConfig.java**

* **Role:** Configures Spring Security for the application.

* **Key functions:**

  * Disables CSRF for simplicity.
  * Defines which endpoints are public (`/api/auth/**`) and which require authentication.
  * Adds the `JwtAuthFilter` to intercept requests and validate JWTs.
  * Configures `AuthenticationManager`.

* **Changes for CORS:**

  ```java
  http.cors().and() // enable CORS
  ```

  And define a `CorsConfigurationSource` bean listing allowed origins (e.g., the frontend served via Python HTTP server). This allows browser-based JS to call the API.

### 2. **AuthController.java**

* **Role:** Handles authentication requests and protected endpoints.
* **Key endpoints:**

  * `POST /api/auth/login`: Authenticates a user and returns a JWT.
  * `GET /api/hello`: Example of a protected endpoint returning a message.

### 3. **JwtAuthFilter.java**

* **Role:** Intercepts every HTTP request to validate the JWT.
* **Key behavior:**

  * Extracts token from the `Authorization` header.
  * Validates token and sets the security context for authenticated users.

### 4. **JwtService.java**

* **Role:** Handles JWT creation, signing, parsing, and validation.
* **Key methods:**

  * `generateToken(String username, Map<String,Object> claims)`: Creates a JWT.
  * `extractUsername(String token)`: Extracts username from token.
  * `isTokenValid(String token, String username)`: Validates token integrity and expiration.

### 5. **MyUserDetailsService.java**

* **Role:** Provides user details for authentication.
* **Implementation:** In-memory user store with two users (`user` and `admin`).

### 6. **AuthRequest / AuthResponse.java**

* **Role:** Data transfer objects (DTOs) for login requests and responses.
* **Implementation:** `AuthRequest` holds `username` and `password`. `AuthResponse` returns `token`.

### 7. **application.properties**

* **Role:** Configures application properties.
* **Key properties:**

  * `app.jwt.secret`: Secret key used for signing JWTs.
  * `app.jwt.expiration-ms`: Token expiration time.
  * `server.port`: Backend server port.

---

## Frontend (Single Page) Integration

### 1. Frontend Overview

* Uses a **single HTML page** (`single-page.html`) with vanilla JS.
* Handles:

  * Login form submission.
  * Storing JWT in `localStorage`.
  * Showing protected content (`/api/hello`) after login.
  * Logout.
* Dynamically switches between login view and dashboard view without reloading the page.

### 2. Running Frontend with Python HTTP Server

1. Navigate to the folder containing `single-page.html`.
2. Start a simple HTTP server:

```bash
python -m http.server 5500
```

3. Open `http://localhost:5500` in a browser.

> **Note:** Ensure that the backend CORS configuration allows `http://localhost:5500`.

### 3. Frontend Logic

* On **login:** Sends a POST request to `/api/auth/login` with JSON `{username, password}`.
* Stores JWT in `localStorage` on success.
* Shows the **dashboard view**, fetches `/api/hello` with `Authorization: Bearer <token>`, and displays the message.
* On **logout:** Clears JWT from `localStorage` and returns to login view.

### 4. CORS Considerations

* Backend must allow requests from the frontend origin:

```java
config.setAllowedOrigins(List.of("http://localhost:5500"));
```

* This allows the browser to make cross-origin AJAX requests to the backend.

---

## Running the Project

### Backend (Spring Boot)

1. Run Spring Boot application (e.g., via `./mvnw spring-boot:run` or your IDE).
2. Server runs on `http://localhost:8080`.

### Frontend (Single Page)

1. Serve `single-page.html` using Python server or place it in `src/main/resources/static` to serve from the same origin.
2. Open the frontend URL in the browser.
3. Login with credentials (`user/password` or `admin/admin123`) and access the dashboard.

<img width="915" height="384" alt="image" src="https://github.com/user-attachments/assets/e8bc4097-104f-40b2-8486-65e6a33f5195" />

#### dashboard
<img width="895" height="279" alt="image" src="https://github.com/user-attachments/assets/24e9bf0e-ec7c-48ff-94b0-40e2daf7f62e" />

---

## Summary

This project demonstrates a full JWT authentication flow:

* Spring Boot backend with JWT security.
* CORS-enabled backend for browser access.
* Vanilla JS frontend SPA for login, storing token, and accessing protected endpoints.
* Simple single-page application with login, dashboard, and logout functionality.

It’s easy to extend:

* Add more protected endpoints.
* Replace in-memory users with a database.
* Improve frontend styling with CSS frameworks (Bootstrap, Tailwind, etc.).

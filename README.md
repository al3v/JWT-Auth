# JWT-Auth

# Authentication in HTTP/HTTPS

## Importance of Authentication

Authentication is the process by which a system or application verifies the identity of a user. This is crucial for several reasons:

1. **Security:**
   - Authentication ensures that only authorized users can access certain data or functionalities. This protects personal information, financial data, and other sensitive information.

2. **User Tracking:**
   - It is essential for tracking which user performed which actions. This allows for effective system management and monitoring of user behavior when needed.

3. **Personalization:**
   - Authentication is used to remember users' personal preferences and settings, improving the user experience.

## Traditional Authentication Methods

1. **Username and Password:**
   - Users log into the system using a username and password. This is the most common method but can have security vulnerabilities (e.g., password theft, weak passwords).

2. **Multi-Factor Authentication (MFA):**
   - In addition to a username and password, another factor is used for authentication. This could be an SMS code, email confirmation, or biometric data (fingerprint, facial recognition). It enhances security.

3. **Session-Based Authentication:**
   - After logging in with a username and password, the server creates a session ID and sends it to the user in a cookie. The user sends this session ID with every request to the server, which verifies it to confirm the user's identity.

## JWT (JSON Web Token) Based Authentication

**What is JWT?**
- JWT is a JSON-formatted, encrypted "token" that contains user information. This token is created by the server and sent to the user.

**Advantages of JWT:**
1. **Stateless:**
   - The server does not need to store session information, reducing server load and increasing scalability.

2. **Security:**
   - JWT is encrypted, ensuring data security. Additionally, the token is signed to verify it has not been altered.

3. **Flexibility:**
   - JWT can be easily used across different applications and services. It is widely preferred in microservice architectures.

## Differences Between JWT and Traditional Methods

1. **Server Load:**
   - In traditional session-based authentication, the server must store session information. With JWT, this requirement is eliminated.

2. **Transmission Ease:**
   - JWT contains user information, making it easy to transmit between different services. In session-based methods, each service must verify session information.

3. **Security and Efficiency:**
   - JWT is encrypted and signed, providing high data security. The server does not perform session verification with each request, speeding up processes.

## Differences Between JWT and Session-Based Authentication

1. **Server Load:**
   - **Session-Based:** The server stores session information in memory or a database, which can increase load and complexity.
   - **JWT-Based:** The server does not store session information. The token itself contains all necessary user information, reducing server load.

2. **State Management:**
   - **Session-Based:** Requires stateful server management, meaning the server must keep track of each user's session.
   - **JWT-Based:** Is stateless, meaning the server does not need to maintain session state. The client's token carries all necessary information.

3. **Scalability:**
   - **Session-Based:** Can become challenging to scale, as session information needs to be shared across servers or a central store must be used.
   - **JWT-Based:** Easier to scale since no session information is stored on the server. Any server can validate the token without needing shared state.

4. **Security:**
   - **Session-Based:** Sessions can be hijacked if cookies are intercepted. Requires additional measures to ensure security.
   - **JWT-Based:** Tokens are signed and can be encrypted, making them more secure. However, if a token is intercepted, it can still be used until it expires.

5. **Transmission Ease:**
   - **Session-Based:** Each request must verify the session ID, which requires a lookup in the server's **session store**.
   - **JWT-Based:** The token is self-contained, and verification is done by checking the token's signature and claims, which can be faster.
## Summary
- Authentication is critical for security, user tracking, and personalization.
- Traditional methods include username and password, multi-factor authentication, and session-based authentication.
- JWT provides secure and efficient authentication without requiring the server to store session information.
- JWT offers flexibility and efficiency, especially in microservice architectures and distributed systems.

This fundamental information helps in understanding the importance of authentication and the advantages of different methods.

# Example: REST API and JWT Authentication

## Overview

In this example, we will secure a REST API application created with Node.js using the JWT (JSON Web Tokens) method.

## Key Points

- **Objective:** Demonstrate how to secure REST API applications so that only authenticated clients can access them.
- **Simplified Storage:** Usernames and hashed passwords will be stored in an array. In real applications, this information should be stored in a database. This is simplified for this example.
- **Technologies Used:** Node.js with the Express server, bcrypt for hashing passwords, and jsonwebtoken for creating and verifying tokens.

## How the System Works

- **Authentication and API Servers:** We will assume we have two servers: one for authentication and one for providing the API. Even though they will run on the same server, they will operate on different ports to separate authentication and API functions.
- **Token Verification:** Both servers will use the same `ACCESS_TOKEN_SECRET` value to sign and verify tokens.

## Detailed Steps

1. **Authentication Server:**
   - Receives `user` and `password` values.
   - Signs the token with `ACCESS_TOKEN_SECRET`.

2. **API Server:**
   - Verifies the token using the same `ACCESS_TOKEN_SECRET`.

By following these steps, any server with the same `ACCESS_TOKEN_SECRET` can validate the JWT token.

## Summary

- **Security:** JWT provides a secure way to authenticate clients accessing the API.
- **Scalability:** Using the same secret key across multiple servers allows for easy token validation.
- **Simplicity:** This example simplifies user data storage, but real applications should use a database.
- **Technologies Used:** We will use the Express server in Node.js, along with the bcrypt and jsonwebtoken modules.

This example helps understand how to implement JWT authentication in a Node.js REST API application, making it accessible only to authenticated users.

## Practice: JWT Authentication with Node.js

### Step 1: Create Project Directory and Initialize

1. Create a directory for your project and navigate into it.
2. Initialize a new Node.js project using `npm init -y`.

```bash
$ mkdir jwt-practice
$ cd jwt-practice/
$ npm init -y
```
### Step 2: Install Required Modules

1. Install the `express` module and `nodemon` and `dotenv` as development dependencies:
- dotenv: This module allows you to load environment variables from a .env file into

```bash
$ npm i express
$ npm i --save-dev nodemon dotenv
```

### Step 3: Install Additional Modules

1. Install the `bcrypt` and `jsonwebtoken` modules:

```bash
$ npm i bcrypt jsonwebtoken
```

### Step 4: Create the `.env` File

1. Install the `bcrypt` and `jsonwebtoken` modules:
- `.env:` This file contains environment variables such as the server port and secret keys for token encryption. 

```bash
$ cat > .env
PORT = 5000
ACCESS_TOKEN_SECRET = 3e9af42de397cfc9387a06972c28ca13ac7e9a60fb6d1f05295bc6057baf500672d4a13db5d04ea84bbc4c5679164a7723fd49f51b7d3dc3dfe3b76c8e
REFRESH_TOKEN_SECRET = 56ad615ad7d2ee09e48096e5d4d156f647433b1afad162311c45aa520697b65d13a5c72891f6145abf267588c61240d2f80b3d681eb462
TOKEN_SERVER_PORT = 4000
```
- CTRL+D to save and exit

- ACCESS_TOKEN_SECRET: The secret key used to create the access token. It is crucial for this key to be unique and randomly generated.
- REFRESH_TOKEN_SECRET: The secret key used to create the refresh token.
- PORT: The port number for the authentication server.
- TOKEN_SERVER_PORT: The port number for the token validation server.

### Step 5: How the System Works

1. **Creating a User:**
   - We will create a user using the `/createUser` method. Normally, users would be stored in a database, but for simplicity, we will store them on the authentication server.
   - The authentication server will not store passwords in plain text. Instead, it will hash the passwords before storing them. It will then return the username and the hashed password.

2. **User Login:**
   - Next, we will log in to the authentication server using the `/login` method.
   - We will provide the username and password. The authentication server will hash the provided password and compare it with the stored hashed password.
   - If the hashed passwords match, the server will issue an access token and a refresh token. These tokens are created using the secrets stored in the `.env` file.


### Step 6: Configuration for Authentication and Token Validation Servers

1. **Servers Setup:**
   - We will have two servers: `authServer.js` (for authentication) and `validateToken.js` (for token validation). Both servers will use the same secrets stored in the `.env` file.
   - If these servers run as microservices, it's crucial to have the same `.env` file in each microservice container. This ensures consistent token validation across services.
   - **Security Note:** The `.env` file contains sensitive information and should be treated as a secret.


### Step 7: Creating the Authentication Server

1. **Create `authServer.js`:**
   - This file will handle user registration and login. It is crucial as it sets up the main logic for authenticating users and issuing JWT tokens.

2. **Importance:**
   - This step is important because it initializes the server that will manage user authentication, hash passwords, and generate secure tokens for verified users.

3. **Create `authServer.js` in the same directory and Add the Following Code:**
```bash
$ touch authServer.js
```

```javascript
require('dotenv').config(); // Load environment variables from .env file
const bcrypt = require('bcrypt'); // Import bcrypt for password hashing
const users = []; // Array to store users

const express = require('express'); // Import express for server creation
const app = express();

app.use(express.json()); // Middleware to parse JSON bodies

const port = process.env.TOKEN_SERVER_PORT; // Get the port number from .env file

app.listen(port, () => {
    console.log(`Authorization Server running on ${port}...`);
});

// REGISTER A USER
app.post('/createUser', async (req, res) => {
    const user = req.body.name;
    const hashedPassword = await bcrypt.hash(req.body.password, 10); // Hash the password
    users.push({ name: user, password: hashedPassword }); // Store the user
    res.status(201).send({ name: user, password: hashedPassword });
    console.log(users);
});
```
- Save the file and run the server using the following command:
```bash
$ node authServer.js
```
- require('dotenv').config();: Loads environment variables from the .env file.
- bcrypt: Library for hashing passwords, ensuring they are not stored in plain text.
- users: Array to store registered users. In real applications, a database should be used.
- express: Framework to create the server and handle routes.
- app.use(express.json());: Middleware to parse JSON bodies in incoming requests.
- app.listen(port, () => {...}): Starts the server on the specified port.
- app.post('/createUser', async (req, res) => {...}): Route to register a new user. It hashes the user's password and stores the user in the users array.



### Step 8: Registering a User via Postman

1. **Start the `authServer.js` Program:**
   - Ensure your `authServer.js` program is running. You should see a message indicating that the Authorization Server is running on the specified port.
  
![image](https://github.com/al3v/JWT-Auth/assets/73062283/efb62806-a253-419b-9811-22a22b8537d6)


2. **Use Postman to Register a User:**
   - Open Postman, a popular tool for testing APIs.
   - Set up a POST request to the following URL:
     ```plaintext
     http://localhost:4000/createUser
     ```

3. **Send JSON Data in the Request Body:**
   - In Postman, under the "Body" tab, select "raw" and choose "JSON" from the dropdown menu.
   - Enter the following JSON data, which includes the user's name and password:
     ```json
     {
       "name": "Levy",
       "password": "password"
     }
     ```

4. **Send the Request:**
   - Click the "Send" button to send the request to the server.
  
   ![image](https://github.com/al3v/JWT-Auth/assets/73062283/378e7408-03ae-44e6-8823-5fd2fca36882)


5. **Server Response:**
   - The server will respond with a JSON object that includes the username and the hashed version of the password. The original password is hashed using bcrypt and stored in the users array.
   - Example response:
     ```json
     {
        "user": "levy",
        "password": "$2b$10$PqFArfTcHxUvDDHLwzYOce/L9PMsofqi2C.Vm5XXFMpCz1BUko7Fe"
     }
     ```







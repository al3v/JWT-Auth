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
   - This hashed password is what the server will use for future authentication requests to verify the user's identity.
   - Instead of storing the plain password we sent, the hashed version will be stored in the `users` array inside `authServer.js`. All verification will be performed using the hashed version of the password.
   - Example response:
     ```json
     {
        "user": "levy",
        "password": "$2b$10$PqFArfTcHxUvDDHLwzYOce/L9PMsofqi2C.Vm5XXFMpCz1BUko7Fe"
     }
     ```



### Step 9: Adding Login Functionality

This update allows us to authenticate users using their hashed passwords. By sending a login request with a username and password, the server verifies the credentials, and if valid, responds with JWT tokens. These tokens are then used for authenticated requests to the server.

1. **Update `authServer.js`:**
   - Add the following code snippet to the end of your `authServer.js` file. This will allow us to log in using the hashed password.

2. **Code to Add:**

```javascript
const jwt = require("jsonwebtoken");

// AUTHENTICATE LOGIN AND RETURN JWT TOKEN
app.post("/login", async (req, res) => {
    const user = users.find(c => c.user === req.body.name);
    // Check to see if the user exists in the list of registered users

    if (user == null) {
        return res.status(404).send("User does not exist!");
    }
    // If user does not exist, send a 404 response

    if (await bcrypt.compare(req.body.password, user.password)) {
        const accessToken = generateAccessToken({ user: req.body.name });
        const refreshToken = generateRefreshToken({ user: req.body.name });
        return res.json({ accessToken: accessToken, refreshToken: refreshToken });
    } else {
        return res.status(401).send("Password Incorrect!");
    }
});

// Helper functions to generate tokens
function generateAccessToken(user) {
    return jwt.sign(user, process.env.ACCESS_TOKEN_SECRET, { expiresIn: '15m' });
}

function generateRefreshToken(user) {
    return jwt.sign(user, process.env.REFRESH_TOKEN_SECRET);
}
```

### Step 10: Adding Token Generation Functions

1. **Update `authServer.js`:**
   - Add the following code snippet to the end of your `authServer.js` file. This part of the code allows us to generate and refresh our JWT tokens.

2. **Code to Add:**
These functions are essential for managing JWT tokens. The generateAccessToken function creates a short-lived token for secure access, while the generateRefreshToken function creates a slightly longer-lived token that can be used to obtain a new access token when the original expires. This mechanism helps maintain security while ensuring that users can continue their sessions without frequent logins.

```javascript
// accessTokens
function generateAccessToken(user) {
    return jwt.sign(user, process.env.ACCESS_TOKEN_SECRET, { expiresIn: "15m" });
}

// refreshTokens
let refreshTokens = [];

function generateRefreshToken(user) {
    const refreshToken = jwt.sign(user, process.env.REFRESH_TOKEN_SECRET, { expiresIn: "20m" });
    refreshTokens.push(refreshToken);
    return refreshToken;
}
```


### Step 11: Adding Logout Functionality
Logging out is an essential security measure. It ensures that once a user finishes their session, their tokens cannot be reused to gain unauthorized access. After logging out, the tokens are invalidated, preventing them from being refreshed or reused. This helps to protect the system and user data from potential misuse or unauthorized access.

1. **Update `authServer.js`:**
   - Add the following code snippet to the end of your `authServer.js` file. This part of the code will allow users to log out by removing their refresh token.

2. **Code to Add:**

```javascript
app.delete("/logout", (req, res) => {
    refreshTokens = refreshTokens.filter(c => c !== req.body.token);
    // Remove the old refreshToken from the refreshTokens list
    res.status(204).send("Logged out!");
});
```

### Step 12: Logging into the System

To log into the system, follow these steps:

1. **Send a JSON Object to the Login Endpoint:**
   - We will send a JSON object containing the `name` and `password` fields to the following endpoint:
     ```plaintext
     http://localhost:4000/login
     ```
![image](https://github.com/al3v/JWT-Auth/assets/73062283/20889568-460f-42b5-b061-b269ce410d34)

2. **Example JSON Data:**
   - The JSON object should look like this:
     ```json
     {
       "name": "levy",
       "password": "password"
     }
     ```

3. **Process:**
   - The server will receive the username and plain text password.
   - It will then hash the provided password and compare it with the stored hashed password.
   - If the hashed passwords match, the server will generate and return an `accessToken` and a `refreshToken`.

4. **Response:**
   - The server will respond with a JSON object containing the `accessToken` and `refreshToken`:
     ```json
     {
       "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiSGFrYW4iLCJpYXQiOjE3MTE0NzQ0ODQsImV4cCI6MTc0MDA4NDQ4MC.ZR6vOoYp7EvUZO_ucMHrRVIJYNTv1OtzEqzQ7OCxn3ANC",
       "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiSGFrYW4iLCJpYXQiOjE3MTE0NzQ0ODQsImV4cCI6MTc0MDA4NTQ0MC.MDp4NfO6_A1ta4DGKzrYGEfvFWM_OciatGE1Xb2MEa5a9uOHCMg"
     }
     ```

### Explanation of the Process

1. **Login Request:**
   - We send our username and password in plain text to the login endpoint. The server receives this data and processes it.

2. **Password Handling:**
   - The server hashes the provided password and compares it with the stored hashed password. This ensures that even if the transmission is intercepted, the passwords remain secure.

3. **Token Generation:**
   - If the passwords match, the server generates an `accessToken` that is valid for 15 minutes and a `refreshToken` that is valid for 20 minutes.
   - The `accessToken` is used for accessing protected resources, while the `refreshToken` is used to obtain a new `accessToken` when the original expires.

4. **Response:**
   - The server responds with the `accessToken` and `refreshToken` in a JSON object. The `accessToken` should be used without the quotation marks for subsequent authenticated requests.

By following these steps, you can log into the system securely, receiving tokens that allow you to access protected resources and refresh your session without needing to log in again frequently. This enhances both security and user experience.


### Step 13: How the System Works

1. **Token Expiration:**
   - When we log in, the access token (`accessToken`) provided to us is valid for 15 minutes. After this 15-minute period, we may receive a "401 Unauthorized" error.

2. **Refreshing the Token:**
   - Upon receiving a "401 Unauthorized" error, we should immediately use the refresh token to obtain a new access token and refresh token. This can be done by sending a request to the `/refreshToken` endpoint.

3. **Time Window for Refreshing the Token:**
   - If we do not refresh the token within 20 minutes, we may need to call the `/login` endpoint again to generate a new access token. It is important to refresh the token before the refresh token itself expires.

4. **Security Considerations:**
   - If unauthorized individuals gain access to our access token, they can use it for 15 minutes before it expires. Therefore, reducing the expiration time can increase security. Adjusting the expiration times based on security requirements can help protect the system from unauthorized access.

### Summary

By understanding how token expiration and refreshing work, you can ensure continuous and secure access to the system. Handling token expiration properly is crucial for maintaining session security and preventing unauthorized access. Implementing these measures helps create a robust authentication system that balances user convenience with strong security practices.





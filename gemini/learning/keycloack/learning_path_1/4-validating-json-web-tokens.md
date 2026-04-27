# Validating JSON Web Tokens (JWT)

That is a very logical guess! In older systems, that is exactly how it worked (often called "opaque tokens" or "session validation"). 

However, Keycloak uses a much faster, modern approach: **JSON Web Tokens (JWTs)**. 

Because your Spring Backend could get thousands of API requests a second, asking Keycloak "is this valid?" every single time would overload the Keycloak server and slow down your API. 

Instead, JWTs are designed for **local, offline validation**. Here is how it works:

### 1. The Cryptographic Signature
When Keycloak creates the Access Token for the user, it "signs" the token using a secret, cryptographic **Private Key** that only Keycloak knows. 

### 2. The Public Key
Keycloak also publishes a **Public Key**. Anyone can see the Public Key, but it can *only* be used to verify signatures created by the matching Private Key. It cannot create new signatures.

### 3. Local Verification by Spring
When your Spring Backend up up, it connects to Keycloak once, downloads the Public Key, and caches it in memory. 

Then, whenever a request comes in with an Access Token, your Spring Backend does the math locally:
*   "Does this token's signature match Keycloak's Public Key?"
*   If **Yes**: Spring knows 100% that Keycloak issued this token and it hasn't been tampered with. It allows the request.
*   If **No**: Spring instantly rejects the request as unauthorized. 

*No network call to Keycloak is required per request!*

### The Anatomy of a JWT
If you were to intercept an Access Token, it just looks like a long string of gibberish. But it's actually just Base64-encoded text split into three parts: `Header.Payload.Signature`.
*   **Header**: Tells Spring what algorithm was used to sign it.
*   **Payload**: Contains the data (who the user is, what roles they have, when the token expires).
*   **Signature**: The cryptographic math proving it's real.

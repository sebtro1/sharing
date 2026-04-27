# Roles and Claims

Perfect! You have completely grasped the flow. 

When a user logs in, Keycloak bundles up information about that user, puts it into the **Payload** of the JWT, signs it, and sends it off. Spring then unpacks that Payload to make authorization decisions.

In the IAM (Identity and Access Management) world, these pieces of information in the payload are called **Claims**.

### What does a Claim look like?
If you decoded the Payload of a JWT, it would look like a simple JSON object:

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "email": "john.doe@example.com",
  "realm_access": {
    "roles": [
      "user",
      "admin"
    ]
  }
}
```

*   `sub`, `name`, `email` are standard claims.
*   The `realm_access.roles` array is the crucial part for your Spring Backend.

### How Keycloak and Spring Work Together

1.  **In Keycloak (The Admin Console):**
    *   You create a user (e.g., John Doe).
    *   You create a Role (e.g., `admin`).
    *   You assign the `admin` Role to John Doe.
2.  **In the JWT:**
    *   Keycloak automatically injects that `admin` role into the `realm_access` claim when John logs in.
3.  **In Spring Boot:**
    *   Spring Security reads the JWT.
    *   It extracts the `admin` role from the payload.
    *   You write code on your endpoints like `@PreAuthorize("hasRole('admin')")`.
    *   If the token has the role, the endpoint executes. If not, Spring returns a `403 Forbidden` error.

Because the JWT is cryptographically signed (which we learned about in the previous step), Spring knows the user didn't just maliciously type `"admin"` into the payload themselves!

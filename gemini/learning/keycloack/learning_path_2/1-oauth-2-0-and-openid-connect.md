# OAuth 2.0 and OpenID Connect

When learning Keycloak, you will constantly hear the terms "OAuth 2.0" and "OpenID Connect" (OIDC). They are often used together, but they serve two fundamentally different purposes: **Authorization** vs **Authentication**.

### 1. OAuth 2.0: The Valet Key (Authorization)

**OAuth 2.0 is an *Authorization* framework.** It is designed to grant an application limited access to a user's resources without giving the application the user's password.

*   **Analogy:** Think of OAuth 2.0 like giving a valet key to a parking attendant. The valet key allows the attendant to drive and park your car (access your resources), but it doesn't allow them to open the trunk or the glovebox. Furthermore, the valet key doesn't tell the attendant *who you are*; it just gives them permission to do a specific task.
*   **What it produces:** Access Tokens.
*   **Limitation:** OAuth 2.0 provides absolutely no standard way to identify the user. It just says "Whoever holds this token is allowed to do X."

### 2. OpenID Connect (OIDC): The ID Badge (Authentication)

Because OAuth 2.0 didn't handle user identity, the industry created **OpenID Connect (OIDC)**. OIDC is a thin identity layer built *on top* of the OAuth 2.0 protocol.

**OIDC is an *Authentication* protocol.** It is designed specifically to answer the question: "Who is the user that is currently using the application?"

*   **Analogy:** Think of OIDC as your company ID badge. When you show your badge to the security guard, they know exactly *who* you are (your name, employee ID, department).
*   **What it produces:** ID Tokens (specifically, JSON Web Tokens or JWTs).

### How They Work Together in Keycloak

Keycloak uses **OpenID Connect** by default. Because OIDC is built on top of OAuth 2.0, when a user logs in via Keycloak, Keycloak actually issues *multiple* tokens simultaneously:

1.  **An ID Token (OIDC):** Your frontend application reads this to know who the user is (e.g., to display "Welcome, Jane!").
2.  **An Access Token (OAuth 2.0):** Your frontend application sends this to your Spring Backend to prove it has permission to make API calls.

When you configure your Spring Backend, you are technically setting it up as an "OAuth 2.0 Resource Server" that focuses on validating that Access Token.

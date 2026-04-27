# The 4 Keycloak Concepts You Need to Know

To make this work, there are four main concepts in Keycloak you will be interacting with:

1.  **Realm:** Think of this as an isolated tenant or "workspace." All your configurations, users, and apps live inside a Realm. You will likely use an existing realm or create one specifically for your company's apps.
2.  **User:** The person or system trying to access your Spring Backend. They exist inside the Realm.
3.  **Client:** *This is the most important concept for your goal.* In Keycloak terminology, any application that wants to use Keycloak to secure itself is a "Client." **Your Spring Backend will be registered as a Client in Keycloak.**
4.  **Roles:** Labels you give to Users (e.g., `admin`, `standard_user`). Your Spring Backend will read these roles from the Access Token to decide *what* the user is allowed to do once they are inside the API.

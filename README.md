# Keycloak Authentication Plugin

This Keycloak authentication plugin is a middleware for Go applications, which manages user authentication using Keycloak, an open-source identity and access management solution.

## Code Explanation

### Structs

The plugin uses several data structures:

- Config: This struct stores the configuration details of the Keycloak instance, such as the URL, client ID, and client secret.
  keycloakAuth: This struct is the main component of the plugin, storing the next HTTP handler and the configuration.
  KeycloakTokenResponse: This struct stores the response from Keycloak when a token is requested.
- state: This struct stores the URL to which the user should be redirected after successful authentication.

### Main Functions

The plugin has several main functions:

- CreateConfig(): Initializes and returns a new Config struct.
- New(): Creates a new Keycloak authentication middleware. It checks the provided configuration and returns an error if the Keycloak URL or client ID is not provided.
- ServeHTTP(): This is the main function of the middleware. It checks for an "Authorization" cookie in the request. If the cookie exists and its value starts with "Bearer ", it verifies the token. If the token is valid, it allows the request to proceed. If the token is invalid, it redirects the request to Keycloak for authentication. If the cookie does not exist, it checks for an authorization code in the request URL. If the code exists, it exchanges it for a token. If the code does not exist, it redirects the request to Keycloak for authentication.
- exchangeAuthCode(): Exchanges an authorization code for a token.
- redirectToKeycloak(): Redirects the request to Keycloak for authentication.
- verifyToken(): Verifies the validity of a token.

## How it Works

When a request is received, the middleware first checks for an "Authorization" cookie. If it exists and the token inside is valid, the request is allowed to proceed.
If the token is invalid or doesn't exist, the middleware checks for an authorization code in the request URL.
If the authorization code exists, the middleware exchanges it for a token, sets this token as a cookie, and redirects the user to their original location.
If the authorization code doesn't exist, the middleware redirects the user to Keycloak for authentication.
The user is then prompted to enter their credentials on the Keycloak login page. After successful authentication, Keycloak redirects the user back to the application with an authorization code.
The middleware then exchanges this code for a token and the process starts over.
By using this middleware, applications can easily integrate with Keycloak for user authentication without having to implement the logic themselves.

## Installation

First, enable the plugins support in your Traefik configuration file (traefik.yml or traefik.toml):

```yaml
experimental:
  plugins:
    keycloakopenid:
      moduleName: "github.com/schaulet/keycloakopenid"
      version: "v0.1.31"
```

Usage
Add the plugin's specific configuration to your Traefik routers:

```yaml
http:
  middlewares:
    my-keycloakopenid:
      plugin:
        keycloakopenid:
          KeycloakURL: "my-keycloak-url.com"
          ClientID: "<CLIENT_ID"
          ClientSecret: "<CLIENT_SECRET"
          KeycloakRealm: "<REALM"
```

The above configuration will authenticate all incoming requests for mywebsite.com with Keycloak.

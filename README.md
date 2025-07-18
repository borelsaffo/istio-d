# istio-d


🛡️ Étape 3 : Istio valide les JWT (et fait l'autorisation)
Tu vas utiliser les ressources RequestAuthentication et AuthorizationPolicy.

1. RequestAuthentication : pour dire à Istio comment vérifier les JWT
yaml
Copier
Modifier
apiVersion: security.istio.io/v1beta1
kind: RequestAuthentication
metadata:
  name: jwt-keycloak
  namespace: my-namespace
spec:
  selector:
    matchLabels:
      app: my-api
  jwtRules:
    - issuer: "https://keycloak.example.com/realms/myrealm"
      jwksUri: "https://keycloak.example.com/realms/myrealm/protocol/openid-connect/certs"
2. AuthorizationPolicy : pour contrôler l’accès selon les claims
Exemple : autoriser uniquement les utilisateurs avec le rôle admin dans le realm.

yaml
Copier
Modifier
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: allow-admins
  namespace: my-namespace
spec:
  selector:
    matchLabels:
      app: my-api
  action: ALLOW
  rules:
    - from:
        - source:
            requestPrincipals: ["https://keycloak.example.com/realms/myrealm/*"]
      when:
        - key: request.auth.claims[realm_access].roles
          values: ["admin"]
🧠 Résumé
Cible	Solution
Authentification mutuelle	Istio + mTLS (identité de service)
Authentification utilisateur	JWT signé par Keycloak
Autorisation	AuthorizationPolicy basée sur les claims JWT
Avantage	Tu n’as pas besoin d’ajouter de lib auth dans tes apps

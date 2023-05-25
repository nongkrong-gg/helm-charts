# Setup
```sh
sops -d metrics-server/values.enc.yml | helm upgrade --install metrics-server bitnami/metrics-server --version 6.4.1 --namespace metrics-server --create-namespace --values -

helm install nginx ingress-nginx/ingress-nginx --version 4.7.0
sops -d cert-manager/values.enc.yml | helm install cert-manager jetstack/cert-manager --version v1.12.0 --namespace cert-manager --create-namespace --values -

sops -d postgresql/values.enc.yml | helm install postgresql bitnami/postgresql --version 12.5.6 --values -
sops -d redis/values.enc.yml | helm install redis bitnami/redis --version 17.11.3 --values -

sops -d nongkrong-api/k8s.enc.yml | kubectl apply -f -

sops -d cert-manager/ingress.enc.yml | kubectl apply -f - # without annotation issuer
sops -d cert-manager/issuer-prod.enc.yml | kubectl apply -f -
sops -d cert-manager/ingress.enc.yml | kubectl apply -f - # with annotation issuer
```

# Performance Test
```sh
sops -d traffic/k8s.enc.yml | kubectl apply -f -
apk add --no-cache wrk
wrk -c 5 -t 5 -d 99999 -H "Connection: Close" -H "Authorization: Bearer {{ jwt_token }}" http://nongkrong-api:8000/api/events/{{ event_id }}
```

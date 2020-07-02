
# gen certs

create certs folder and go into it

```bash
# create root certificate
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -subj '/O=kubermatic training/CN=kubermatic.training' -keyout kubermatic.training.key -out kubermatic.training.crt

# create csr and key
openssl req -out frontend.kubermatic.training.csr -newkey rsa:2048 -nodes -keyout frontend.kubermatic.training.key -subj "/CN=frontend.kubermatic.training/O=kubermatic training"

# create certificate
openssl x509 -req -days 365 -CA kubermatic.training.crt -CAkey kubermatic.training.key -set_serial 0 -in frontend.kubermatic.training.csr -out frontend.kubermatic.training.crt
```

# create secret
kubectl create -n istio-system secret tls frontend.kubermatic.training --key=frontend.kubermatic.training.key --cert=frontend.kubermatic.training.crt

apply yaml files

go into certs folder

# works
curl -v -H "Host: frontend.kubermatic.training" --resolve "frontend.kubermatic.training:443:$INGRESS_HOST"  --cacert kubermatic.training.crt "https://frontend.kubermatic.training:443/"

# works too
curl -v --resolve "frontend.kubermatic.training:443:$INGRESS_HOST"  --cacert kubermatic.training.crt "https://frontend.kubermatic.training:443/"


verify in kiali
graph display security

# cleanup
kubectl -n istio-system delete secret frontend.kubermatic.training
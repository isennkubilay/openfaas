
## Kubernetes OpenFaas Installation

1. ```
   kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
   ```
2. ```
   helm repo add openfaas https://openfaas.github.io/faas-netes/
   helm repo update
   ```
3. ```
   # generate a random password
   PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)
   kubectl -n openfaas create secret generic basic-auth \
   --from-literal=basic-auth-user=admin \
   --from-literal=basic-auth-password="$PASSWORD"
   ```
4. ```
   helm upgrade openfaas --install openfaas/openfaas \
      --namespace openfaas  \
      --set basic_auth=true \
      --set functionNamespace=openfaas-fn \
      --set serviceType=NodePort
   ```
5. ```
   kubectl get svc -n openfaas gateway-external -o wide
   ```
6. ```
   $ curl -sSL https://cli.openfaas.com | sudo -E sh
   ```

7. ```
   export OPENFAAS_URL=http://ip:nodePort/  
   echo -n $PASSWORD | faas-cli login -g $OPENFAAS_URL -u='admin' --password='112321321opqlzjq21312314'
   ```
8. ```
   curl -X POST http://ip:nodePort/function/figlet -d "Hello openfaas"

   ```

## Astronaut-Finder Function Deploy 

We'll create a function called astronaut-finder that pulls in a random name of someone in space aboard the International Space Station (ISS). Data from: [astros](http://api.open-notify.org/astros.json)

1. `faas-cli new --lang python3 astronaut-finder --prefix="kisen"`  
2. `faas-cli build -f ./astronaut-finder.yml`
3. `faas-cli push -f ./astronaut-finder.yml`
4. `faas-cli deploy -f ./astronaut-finder.yml`
5. `echo | faas-cli invoke astronaut-finder --gateway="ip:nodePort"`
   
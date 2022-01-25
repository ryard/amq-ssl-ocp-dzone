# Deploying the AMQ SSL on Openshift 4 Using template and exposing it to external world.

1 - Set up the keystore and trustore using the certs.sh(take a note of password you typed).
```
  $ ./certs.sh
```
2 - Create two secrets, one to store the certificate and the second to store the broker credentials.
```
$ oc create secret generic amq-credential-secret --from-literal username=admin --from-literal password=admin --from-literal  amq_truststore_password=<password_typed_to_create_certs> --from-literal amq_keystore_password=<password_typed_to_create_certs>

$ oc create secret generic ${BROKER_NAME}-app-secret --from-file=broker.ks --from-file=broker.ts -n ${MYPROJECT}
```
3 - Create the template.
```
$ oc create -f https://raw.githubusercontent.com/rafamqrs/amq-ssl-ocp-dzone/main/template/amq-broker-74-ssl.yaml
```
4 - Deploy the broker.
```
$ oc new-app --name amq-dev-broker-74-ssl --template=amq-broker-74-ssl \
-p AMQ_NAME=amq-dev -p APPLICATION_NAME=amq-broker \
-p AMQ_PROTOCOL="openwire,amqp,stomp,mqtt,hornetq" \ 
-p AMQ_QUEUES=<queue_name> -p AMQ_USER=admin \ 
-p AMQ_PASSWORD=dzone123 -p AMQ_SECRET=amq-dev-app-secret \  
-p AMQ_TRUSTSTORE=broker.ts -p AMQ_KEYSTORE=broker.ks \
-p AMQ_REQUIRE_LOGIN=true \ 
```

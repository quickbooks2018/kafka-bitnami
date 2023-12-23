# Kafka Bitnami

- eks blue prints https://github.com/quickbooks2018/aws-eks-blueprints
- mongo atlas     https://github.com/quickbooks2018/mongodb.git

- Kind Cluster bash script

- https://github.com/quickbooks2018/kind-nginx-ingress

- Kind Ubuntu Cluster include packages (helm kubectl docker kind)

- AmazonLinux bash script
```bash
#!/bin/bash
# Purpose: Kubernetes Cluster
# extraPortMappings allow the local host to make requests to the Ingress controller over ports 80/443
# node-labels only allow the ingress controller to run on a specific node(s) matching the label selector
# https://kind.sigs.k8s.io/docs/user/ingress/


######################
# Docker Installation
#####################
yum install -y docker
systemctl enable docker
systemctl start docker


#######################
# Kubectl Installation
#######################
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
mv ./kubectl /usr/bin/kubectl
kubectl version --client

####################
# Helm Installation
####################
# https://helm.sh/docs/intro/install/

curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
cp /usr/local/bin/helm /usr/bin/helm
rm -f get_helm.sh
helm version

###################
# Kind Installation
###################
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
# curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.0/kind-linux-amd64

# Latest Version
# https://github.com/kubernetes-sigs/kind
# curl -Lo ./kind "https://kind.sigs.k8s.io/dl/v0.9.0/kind-$(uname)-amd64"
# curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.9.0/kind-linux-amd64
# curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.8.1/kind-linux-amd64
chmod +x ./kind

mv ./kind /usr/bin

#############
# Kind Config
#############
#cat << EOF > kind-config.yaml
#kind: Cluster
#apiVersion: kind.x-k8s.io/v1alpha4
#networking:
#  apiServerAddress: 0.0.0.0
#  apiServerPort: 8443
#EOF
  
# kind create --name cloudgeeks cluster --config kind-config.yaml --image kindest/node:v1.21.10
  
  # ssh -N -L 8443:0.0.0.0:8443 cloud_user@d8d0041c.mylabserver.com
  
 # export KUBECONFIG=".kube/config"
  
 ####################  
 # Multi-Node Cluster
 ####################
cat > kind-config.yaml <<EOF
# three node (two workers) cluster config
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
networking:
  apiServerAddress: 0.0.0.0
  apiServerPort: 8443
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
        eviction-hard: "memory.available<5%"
        system-reserved: "memory=1Gi"
        kube-reserved: "memory=1Gi"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
- role: worker
  kubeadmConfigPatches:
  - |
    kind: JoinConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        eviction-hard: "memory.available<5%"
        system-reserved: "memory=1Gi"
        kube-reserved: "memory=1Gi"
- role: worker
  kubeadmConfigPatches:
  - |
    kind: JoinConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        eviction-hard: "memory.available<5%"
        system-reserved: "memory=1Gi"
        kube-reserved: "memory=1Gi"
- role: worker
  kubeadmConfigPatches:
  - |
    kind: JoinConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        eviction-hard: "memory.available<5%"
        system-reserved: "memory=1Gi"
        kube-reserved: "memory=1Gi"        
EOF
  
kind create --name cloudgeeks cluster --config kind-config.yaml --image kindest/node:v1.25.11
 
 
export KUBECONFIG=".kube/config"
 
#End
```

- Ubuntu bash script
```bash
#!/bin/bash
# Purpose: Kubernetes Cluster
# extraPortMappings allow the local host to make requests to the Ingress controller over ports 80/443
# node-labels only allow the ingress controller to run on a specific node(s) matching the label selector
# https://kind.sigs.k8s.io/docs/user/ingress/


######################################
# Docker & Docker Compose Installation
######################################
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
rm -f get-docker.sh


#######################
# Kubectl Installation
#######################
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
mv ./kubectl /usr/local/bin/kubectl
kubectl version --client

####################
# Helm Installation
####################
# https://helm.sh/docs/intro/install/

curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
cp /usr/local/bin/helm /usr/bin/helm
rm -f get_helm.sh
helm version

###################
# Kind Installation
###################
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
# curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.0/kind-linux-amd64

# Latest Version
# https://github.com/kubernetes-sigs/kind
# curl -Lo ./kind "https://kind.sigs.k8s.io/dl/v0.9.0/kind-$(uname)-amd64"
# curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.9.0/kind-linux-amd64
# curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.8.1/kind-linux-amd64
chmod +x ./kind

mv ./kind /usr/local/bin

#############
# Kind Config
#############
#cat << EOF > kind-config.yaml
#kind: Cluster
#apiVersion: kind.x-k8s.io/v1alpha4
#networking:
#  apiServerAddress: 0.0.0.0
#  apiServerPort: 8443
#EOF
  
# kind create --name cloudgeeks cluster --config kind-config.yaml --image kindest/node:v1.21.10
  
  # ssh -N -L 8443:0.0.0.0:8443 cloud_user@d8d0041c.mylabserver.com
  
 # export KUBECONFIG=".kube/config"
  
 ####################  
 # Multi-Node Cluster
 ####################
cat > kind-config.yaml <<EOF
# three node (two workers) cluster config
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
networking:
  apiServerAddress: 0.0.0.0
  apiServerPort: 8443
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
        eviction-hard: "memory.available<5%"
        system-reserved: "memory=1Gi"
        kube-reserved: "memory=1Gi"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
- role: worker
  kubeadmConfigPatches:
  - |
    kind: JoinConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        eviction-hard: "memory.available<5%"
        system-reserved: "memory=1Gi"
        kube-reserved: "memory=1Gi"
- role: worker
  kubeadmConfigPatches:
  - |
    kind: JoinConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        eviction-hard: "memory.available<5%"
        system-reserved: "memory=1Gi"
        kube-reserved: "memory=1Gi"
- role: worker
  kubeadmConfigPatches:
  - |
    kind: JoinConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        eviction-hard: "memory.available<5%"
        system-reserved: "memory=1Gi"
        kube-reserved: "memory=1Gi"        
EOF
  
 kind create --name cloudgeeks cluster --config kind-config.yaml --image kindest/node:v1.25.11
 
 
export KUBECONFIG=".kube/config"
 
#End
```

- Helm commands for apache kafka chart and versions

- Adding bitnami repository to local helm repository
```bash
helm repo ls
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm search repo bitnami
helm search repo bitnami/kafka
helm show values bitnami/kafka
helm search repo bitnami/kafka --versions
helm show chart bitnami/kafka --version 23.0.7
helm show readme bitnami/kafka --version 23.0.7
helm show values bitnami/kafka --version 23.0.7
helm show all bitnami/kafka --version 23.0.7
```

- kafka-values.yaml
```bash
image:
  debug: true
replicaCount: 3
tolerations: 
   - key: "node-role.kubernetes.io/control-plane"
     operator: "Equal"
     value: ""
     effect: "NoSchedule"
externalAccess:
  enabled: false
  autoDiscovery:
    enabled: true
kraft:
  enabled: false    
extraEnvVars:
  - name: KAFKA_ENABLE_KRAFT
    value: "false"
  - name: KAFKA_CFG_DELETE_TOPIC_ENABLE
    value: "true"  # Add this line to enable topic deletion
  - name: KAFKA_CFG_AUTO_CREATE_TOPICS_ENABLE
    value: "true"  # Add this line to enable topic auto-creation
zookeeper:
  enabled: true
  replicaCount: 3
```

- metrics server installation
```bash
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/
helm repo update
helm upgrade --install --set args={--kubelet-insecure-tls} metrics-server metrics-server/metrics-server --namespace kube-system
```

- Helm Apache kafka Installation 
```bash
helm -n kafka upgrade --install kafka-release bitnami/kafka --create-namespace --set persistence.size=8Gi,logPersistence.size=8Gi,volumePermissions.enabled=true,persistence.enabled=true,logPersistence.enabled=true,serviceAccount.create=true,rbac.create=true --version 23.0.7 -f kafka-values.yaml
```

- Login to kafka pod 
```bash
kubectl exec --tty -i kafka-release-0 --namespace kafka -- bash
```

- Kafka Topic creation
```bash
kafka-topics.sh --create --topic test1 --bootstrap-server kafka-release.kafka.svc.cluster.local:9092
```

- Headless Service
```bash
"headless" service in Kubernetes is a service that doesn't have a ClusterIP, meaning it doesn't provide load-balancing or reliable DNS resolution for the service. When you create a headless service, Kubernetes does not allocate a virtual IP for it. Instead, it allows you to directly reach the pods backing the service by enabling DNS resolution directly to the Pod IPs.
```

- Kafka verify the cluster using producer and consumer
```bash
kafka-console-producer.sh --broker-list kafka-release-0.kafka-release-headless.kafka.svc.cluster.local:9092,kafka-release-1.kafka-release-headless.kafka.svc.cluster.local:9092,kafka-release-2.kafka-release-headless.kafka.svc.cluster.local:9092 --topic test1
```

```bash
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test1 --from-beginning
```

### Kafka Management with WEBUI
- kafdrop
```bash
git clone https://github.com/obsidiandynamics/kafdrop.git
```

- kafdrop-values.yaml
```
replicaCount: 1

image:
  repository: obsidiandynamics/kafdrop
  tag: latest
  pullPolicy: Always

kafka:
  brokerConnect: kafka-release-headless.kafka.svc.cluster.local:9092 # Updated to use the bootstrap service
  properties: ""
  truststore: ""
  keystore: ""
  propertiesFile: "kafka.properties"
  truststoreFile: "kafka.truststore.jks"
  keystoreFile: "kafka.keystore.jks"

host:

jvm:
  opts: ""
jmx:
  port: 8686

nameOverride: ""
fullnameOverride: ""

cmdArgs: ""

global:
  kubeVersion: ~

server:
  port: 9000
  servlet:
    contextPath: /

service:
  annotations: {}
  type: ClusterIP
  port: 9000
  #nodePort: 30900

ingress:
  enabled: false
  annotations: {}
  apiVersion: ~
  #ingressClassName: ~
  path: /
  #pathType: ~
  hosts: []
  tls: []

resources:
  # limits:
  #  cpu: 100m
  #  memory: 128Mi
  requests:
    cpu: 1m
    memory: 128Mi

nodeSelector: {}

tolerations: []

affinity: {}

podAnnotations: {}

hostAliases: []

mountProtoDesc:
  enabled: false
  hostPath:
```
```bash
helm -n kafka upgrade --install kafdrop --create-namespace -f kafdrop-values.yaml ./kafdrop/chart/ --wait
```

### Best Solution is Kafdrop

- Kafka Debizium for CDC Change Data Capture
- https://github.com/snapp-incubator/debezium-chart/tree/master
```bash
git clone https://github.com/snapp-incubator/debezium-chart.git
```

- debezium-values.yaml
```yaml
nameOverride: ""
fullnameOverride: "debezium"

connect:
  replicaCount: 1
  imagePullSecrets: [ ]

  # https://quay.io/repository/debezium/connect?tab=tags
  image:
    repository: quay.io/debezium/connect
    pullPolicy: Always
    tag: "2.3.2.Final"

  service:
    type: ClusterIP
    port: 8083
    protocol: TCP
    name: http

  ingress:
    enabled: false

  autoscaling:
    enabled: false

  resources:
    limits:
      cpu: 500m
      memory: 1Gi
    requests:
      cpu: 500m
      memory: 1Gi

  env:
    - name: BOOTSTRAP_SERVERS
      value: kafka-release-headless.kafka.svc.cluster.local:9092
    - name: GROUP_ID
      value: "1"
    - name: CONFIG_STORAGE_TOPIC
      value: debezium_connect_configs
    - name: OFFSET_STORAGE_TOPIC
      value: debezium_connect_offsets
    - name: STATUS_STORAGE_TOPIC
      value: debezium_connect_statuses

ui:
  enabled: true
  replicaCount: 1
  imagePullSecrets: [ ]

  # https://quay.io/repository/debezium/debezium-ui?tab=tags
  image:
    repository: debezium/debezium-ui
    pullPolicy: Always
    tag: "2.1.2.Final"

  service:
    type: ClusterIP
    port: 8080
    protocol: TCP
    name: http

  ingress:
    enabled: false
    router: private
    host: DEBEZIUM_UI_HOST_ADDRESS

  autoscaling:
    enabled: false

  resources:
    limits:
      cpu: 500m
      memory: 1Gi
    requests:
      cpu: 500m
      memory: 1Gi

  env:
    - name: KAFKA_CONNECT_URIS
      value: "http://debezium-connect.kafka.svc.cluster.local:8083"
```
```bash
helm -n kafka upgrade --install debezium --create-namespace -f debezium-values.yaml ./debezium-chart/debezium/ --wait
```

- kubernetes NOSQL Client
```bash
kubectl run mongo-webui --port 3000 --image mongoclient/mongoclient:latest -n default
```

- Create a Mongo Atlas Connector for CDC
- Simply get connection string value from Mongo Atlas and replace it with USERNAME and PASSWORD
```json
{
  "name": "mongo-atlas",
  "config": {
    "connector.class": "io.debezium.connector.mongodb.MongoDbConnector",
    "topic.prefix": "dev.test",
    "mongodb.connection.string": "mongodb+srv://quickbooks2018:CNaTi1Hukm552DYW@cluster-devops-0.rzkdltt.mongodb.net/",
    "mongodb.hosts": "cluster-devops-0.rzkdltt.mongodb.net",
    "mongodb.user": "username",
    "mongodb.password": "password",
    "mongodb.ssl.enabled": true,
    "mongodb.authsource": "testStream",
    "snapshot.mode": "never",
    "provide.transaction.metadata": true,
    "sanitize.field.names": true,
    "heartbeat.interval.ms": 1,
    "cursor.max.await.time.ms": 0
  }
}
```
- Create connector, status check & delete command with curl
- Note: Make sure to port forward the connector port 8083 to localhost:8083
```bash
curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connectors/ -d @/mnt/mongo-atlas-connector.json

curl -i -X GET -H "Accept:application/json" localhost:8083/connectors/mongodb-atlas-connector/status

curl -i -X DELETE -H "Accept:application/json" localhost:8083/connectors/mongodb-atlas-connector
```


- Kafka Management with Akhq
- https://github.com/tchiotludo/akhq/tree/dev/helm/akhq
- https://akhq.io/docs/configuration/helm.html
- https://github.com/tchiotludo/akhq/tree/dev

- Configuration
- https://akhq.io/docs/configuration/helm.html#aws-msk-with-basic-authentication-and-alb-controller-ingress
- https://akhq.io/docs/configuration/authentifications/jwt.html#configuration-file

- tested version 0.23.0

- password https://akhq.io/docs/configuration/authentifications/basic-auth.html

- https://codebeautify.org/sha256-hash-generator

- If you're on a Unix-like system, you can also generate bcrypt hashes directly from the command line using htpasswd, which is part of Apache's httpd tools:
```bash
micronaut:
  security:
    enabled: true
akhq.security:
  basic-auth:
    - username: admin
      password: "<SHA-256 hashed password>"
      groups:
      - admin
    - username: reader
      password: "<SHA-256 hashed password>"
      groups:
      - reader
```
- Note: Update chart version in values.yaml file to 0.23.0

- configuration.yaml put this in akhq-values.yaml file
```yaml
configuration:
  micronaut:
    security:
      enabled: true
      token:
        jwt:
          signatures:
            secret:
              generator:
                secret: '123456789'
    server:
      cors:
        enabled: true
        configurations:
          all:
            allowedOrigins:
              - "*"
  akhq:
    connections:
      local:
        properties:
          bootstrap.servers: "PLAINTEXT://kafka-release-headless.kafka.svc.cluster.local:9092"
```

```bash
git clone https://github.com/tchiotludo/akhq.git
```
```helm
helm upgrade --install kafka-akhq --namespace kafka \
  -f akhq-values.yaml --create-namespace --wait ./akhq/helm/akhq
```

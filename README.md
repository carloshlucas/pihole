# PIHOLE

This project shows how to install and configure Pihole on your environment to block ads using Kubernetes.

I assume that you already have your Kubernetes cluster up and running!

## My Lab

* Windows Server as DHCP
* DNS Server (Forwarding the consult to Pihole)
* Kubernetes
* MetalLB
* NGINX Controller

## Step by Step

1. Namespace
2. Persistent Volume
3. Persistent Volume Claim
4. Ingress (Ingress Controller must be deployed)
5. Pi-hole Deploy via Helm
6. Generate Certificate

## Create the objects (Step 1,2,3 and 4)

Apply the yaml file below to you Kubernetes Cluster

```
kubectl apply -f pihole.yaml
```

## Pi-hole Deploy via Helm

**ADD HEML CHARTS**
```
helm repo add mojo2600 https://mojo2600.github.io/pihole-kubernetes/
helm repo update
```

**INSTALLING HELM CHARTS**
```
helm install pihole mojo2600/pihole --namespace pihole --values pihole.values.yml
```

### GENERATE CERTIFICATE

*** This step is not mandatory ***

Procedure to create CSR with SAN
Login into a server where you have OpenSSL installed
Go to /tmp or create any directory
Create a file named san.cnf using vi (if on Unix) with the following information

```
[ req ]
default_bits       = 2048
distinguished_name = req_distinguished_name
req_extensions     = req_ext
[ req_distinguished_name ]
countryName                 = Country Name (2 letter code)
stateOrProvinceName         = State or Province Name (full name)
localityName               = Locality Name (eg, city)
organizationName           = Organization Name (eg, company)
commonName                 = Common Name (e.g. server FQDN or YOUR name)
[ req_ext ]
subjectAltName = @alternative_names
[alternative_names]
DNS.1   = chlabpihole.chlab.local
DNS.2   = 192.168.15.180

Note: alt_names section is the one you have to change for additional DNS.
```

Save the file and execute the following OpenSSL command, which will generate CSR and KEY file

```
openssl req -newkey rsa:2048 -nodes -out tls.csr -keyout tls.key -config san.cnf
```

##### References

https://github.com/MoJo2600/pihole-kubernetes

https://kauri.io/68-selfhost-pihole-on-kubernetes-and-block-ads-and/5268e3daace249aba7db0597b47591ef/a

https://geekflare.com/san-ssl-certificate/




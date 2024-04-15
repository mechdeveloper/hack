# hack

az group create --name ashishbaghel-rg --location eastus
az deployment group create -g ashishbaghel-rg -n LinuxBuildVM -f docker-build-machine-vm.json -p docker-build-machine-vm.parameters.json

ssh -p 2266 wthadmin@<VM Public IP>
ssh -p 2266 wthadmin@40.121.197.143
docker run -d -p 3000:3000 ab-content-web
docker run -d -p 3001:3001 ab-content-api
 
docker run -d --name api --network fabmedical -p 3001:3001 ab-content-api
docker run -d --name web --network fabmedical -p 3000:3000 ab-content-web
API
```Dockerfile
# Use official Node.js image as base
FROM node:8
# Set working directory inside the container
WORKDIR /usr/src/app
# Copy package.json and package-lock.json to the working directory
COPY package*.json ./
# Install dependencies
RUN npm install
# Copy the rest of the application code
COPY . .
# Expose the port that the app runs on
EXPOSE 3001
# Command to run the application
CMD ["node", "server.js"]
```
WEB
```Dockerfile
# Use official Node.js image as base
FROM node:8
# Set working directory inside the container
WORKDIR /usr/src/app
# Copy package.json and package-lock.json to the working directory
COPY package*.json ./
# Install dependencies
RUN npm install
# Copy the rest of the application code
COPY . .
# Expose the port that the app runs on
EXPOSE 3000
# Set the environment variable
ENV CONTENT_API_URL=http://api:3001
# Command to run the application
CMD ["node", "server.js"]
```
docker build -t ab-content-web:v1 .
docker build -t ab-content-api:v1 .
az login --use-device-code
az account set --subscription "f7dd70d0-621a-4007-9d97-ddf6e6a0aa1c"
az acr create --resource-group ashishbaghel-rg --name abacr8786765 --sku Basic
az acr login --name abacr8786765
 
abacr8786765.azurecr.io
docker tag ab-content-web:v1 abacr8786765.azurecr.io/ab-content-web:v1 
docker tag ab-content-api:v1 abacr8786765.azurecr.io/ab-content-api:v1
docker push abacr8786765.azurecr.io/ab-content-web:v1
docker push abacr8786765.azurecr.io/ab-content-api:v1
 
 
az aks create \
  --resource-group ashishbaghel-rg \
  --name abhackaks \
  --enable-managed-identity \
  --node-count 3 \
  --location eastus \
  --zones 1 2 \
  --network-plugin kubenet \
  --attach-acr abacr8786765.azurecr.io \
  --generate-ssh-keys
  
az aks create \
  --resource-group ashishbaghel-rg \
  --name abhackaks1 \
  --enable-managed-identity \
  --node-count 3 \
  --location eastus \
  --zones 1 2 \
  --network-plugin kubenet \
  --attach-acr abacr8786765 \
  --generate-ssh-keys
az aks delete \
  --name abhackaks \
  --resource-group ashishbaghel-rg
  
az aks get-credentials \
  --name abhackaks1 \
  --resource-group ashishbaghel-rg
az aks browse \
  --resource-group ashishbaghel-rg \
  --name abhackaks1


abacr8786765.azurecr.io/ab-content-web:v1
abacr8786765.azurecr.io/ab-content-api:v1


kubectl apply -f api-deployment.yaml

kubectl apply -f web-deployment.yaml

kubectl apply -f web-service.yaml

kubectl apply -f api-service.yaml

kubectl exec -it <pod-name> -- /bin/bash

kubectl get pods


curl http://localhost:3001/speakers


az aks nodepool scale \
  --resource-group ashishbaghel-rg \
  --cluster-name abhackaks1 \
  --name nodepool1 --node-count 1

kubectl exec -it mongodb-deployment-df9f97fdf-5bgs9 -- mongosh "--version"

kubectl exec -it mongodb-deployment-df9f97fdf-5bgs9 -- mongosh
show dbs


whatthehackmsft/content-web:v2
whatthehackmsft/content-api:v2

kubectl set image deployment/web-deployment web-container=whatthehackmsft/content-web:v2
kubectl set image deployment/api-deployment api-container=whatthehackmsft/content-api:v2

kubectl get pods -w


kubectl get events --watch


kubectl rollout undo deployment/api-deployment
kubectl rollout undo deployment/web-deployment

kubectl delete deployment api-deployment 
kubectl delete deployment web-deployment

kubectl delete service web


kubectl exec -it mongodb-deployment-df9f97fdf-tf7hg -- mongosh
show dbs

kubectl delete deployment mongodb-deployment 

kubectl scale deployment api-deployment-blue --replicas=0






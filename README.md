# Blue-Green Deployment of BankApp (Kubernetes)

This project demonstrates a **Blue-Green Deployment strategy** using Kubernetes, applied to a Spring Boot-based banking application – `bankapp`.

Two identical environments (`blue` and `green`) are deployed with the ability to seamlessly switch traffic between them.

---

## What is Blue-Green Deployment?

**Blue-Green Deployment** is a release management strategy where:

- Two versions of your application (Blue and Green) run in parallel.
- Blue is the live version; Green is the new version.
- Once the Green environment is tested and verified, traffic is routed to it.
- If anything goes wrong, switching back to Blue is quick and simple.

---

##  Project Structure


---

## 🛠 Kubernetes Deployment YAMLs

### 🔵 `bankapp-blue.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bankapp-blue
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bankapp
      version: blue
  template:
    metadata:
      labels:
        app: bankapp
        version: blue
    spec:
      containers:
      - name: bankapp
        image: adijaiswal/bankapp:blue
        ports:
        - containerPort: 8080
        env:
        - name: SPRING_DATASOURCE_URL
          value: jdbc:mysql://mysql-service:3306/bankappdb?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
        - name: SPRING_DATASOURCE_USERNAME
          value: root
        - name: SPRING_DATASOURCE_PASSWORD
          value: Test@123
        resources:
          requests:
            memory: "500Mi"
            cpu: "500m"
          limits:
            memory: "1000Mi"
            cpu: "1000m"
```

## How to Switch Traffic
```
apiVersion: v1
kind: Service
metadata:
  name: bankapp-service
spec:
  selector:
    app: bankapp
    version: blue  # Change this to green to switch environments
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
  type: LoadBalancer
```
```
kubectl apply -f service.yaml
```
  ##  Deploy both environments:
```
kubectl apply -f deployments/bankapp-blue.yaml
kubectl apply -f deployments/bankapp-green.yaml

```
```
kubectl apply -f service.yaml  # with version: blue
```





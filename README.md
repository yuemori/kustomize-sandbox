# kustomize sandbox

## example

### run `kustomize build base`

```yaml
apiVersion: v1
kind: Deployment
metadata:
  labels:
    app: example
  name: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - image: nginx:1.11
        name: nginx
        ports:
        - containerPort: 80
---
apiVersion: apps/v1
kind: Service
metadata:
  labels:
    app: example
  name: nginx
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: example
    run: nginx
  type: NodePort
```

### run `kustomize build overlays/production`

```yaml
apiVersion: v1
kind: Deployment
metadata:
  annotations:
    note: Hello, I am production
  labels:
    app: example
    variant: prod
  name: prod-nginx
spec:
  replicas: 80
  selector:
    matchLabels:
      app: example
      variant: prod
  template:
    metadata:
      annotations:
        note: Hello, I am production
      labels:
        app: example
        variant: prod
    spec:
      containers:
      - image: nginx:1.11
        name: nginx
        ports:
        - containerPort: 80
---
apiVersion: apps/v1
kind: Service
metadata:
  annotations:
    note: Hello, I am production
  labels:
    app: example
    variant: prod
  name: prod-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: example
    run: nginx
    variant: prod
  type: NodePort
```

### run `kustomize diff overlays/production`

```diff
diff -u -N /var/folders/7v/qlkdgw4s6zv65pfzyxg774580000gp/T/noop-166343551/apps_v1_Service_nginx.yaml /var/folders/7v/qlkdgw4s6zv65pfzyxg774580000gp/T/transformed-820219040/apps_v1_Service_nginx.yaml
--- /var/folders/7v/qlkdgw4s6zv65pfzyxg774580000gp/T/noop-166343551/apps_v1_Service_nginx.yaml	2018-05-31 12:21:38.000000000 +0900
+++ /var/folders/7v/qlkdgw4s6zv65pfzyxg774580000gp/T/transformed-820219040/apps_v1_Service_nginx.yaml	2018-05-31 12:21:38.000000000 +0900
@@ -1,9 +1,12 @@
 apiVersion: apps/v1
 kind: Service
 metadata:
+  annotations:
+    note: Hello, I am production
   labels:
     app: example
-  name: nginx
+    variant: prod
+  name: prod-nginx
 spec:
   ports:
   - port: 80
@@ -12,4 +15,5 @@
   selector:
     app: example
     run: nginx
+    variant: prod
   type: NodePort
diff -u -N /var/folders/7v/qlkdgw4s6zv65pfzyxg774580000gp/T/noop-166343551/v1_Deployment_nginx.yaml /var/folders/7v/qlkdgw4s6zv65pfzyxg774580000gp/T/transformed-820219040/v1_Deployment_nginx.yaml
--- /var/folders/7v/qlkdgw4s6zv65pfzyxg774580000gp/T/noop-166343551/v1_Deployment_nginx.yaml	2018-05-31 12:21:38.000000000 +0900
+++ /var/folders/7v/qlkdgw4s6zv65pfzyxg774580000gp/T/transformed-820219040/v1_Deployment_nginx.yaml	2018-05-31 12:21:38.000000000 +0900
@@ -1,18 +1,25 @@
 apiVersion: v1
 kind: Deployment
 metadata:
+  annotations:
+    note: Hello, I am production
   labels:
     app: example
-  name: nginx
+    variant: prod
+  name: prod-nginx
 spec:
-  replicas: 2
+  replicas: 80
   selector:
     matchLabels:
       app: example
+      variant: prod
   template:
     metadata:
+      annotations:
+        note: Hello, I am production
       labels:
         app: example
+        variant: prod
     spec:
       containers:
       - image: nginx:1.11
```

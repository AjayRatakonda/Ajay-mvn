### **1. `NoSchedule` Taint with Example YAML**  



#### **Step 1: Taint a Node with `NoSchedule`**
```sh
kubectl taint nodes worker-node-1 key=value:NoSchedule
```
- This prevents **all new pods** from being scheduled on `worker-node-1` **unless they have a matching toleration**.

#### **Step 2: Create a Pod with a Matching Toleration**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: noschedule-pod
spec:
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
  containers:
  - name: nginx-container
    image: nginx
```
- This pod has a **toleration that matches the taint** on `worker-node-1`, so it **can be scheduled on that node**.

#### **Step 3: Apply the Pod YAML**
```sh
kubectl apply -f noschedule-pod.yaml
```

#### **Step 4: Remove the `NoSchedule` Taint (If Needed)**
```sh
kubectl taint nodes worker-node-1 key=value:NoSchedule-
```
This command removes the taint and allows all pods to be scheduled on `worker-node-1`.

---

### **2. `PreferNoSchedule` Taint with Example YAML**  

#### **Step 1: Taint a Node with `PreferNoSchedule`**
```sh
kubectl taint nodes worker-node-2 key=value:PreferNoSchedule
```
- This **prefers** not to schedule new pods on `worker-node-2`, but if no other nodes are available, it **may still allow** scheduling.

#### **Step 2: Create a Pod Without a Toleration**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: prefernoschedule-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
```
- Since this pod **does not** have a toleration, Kubernetes **will try to schedule it on another node**.  
- If no other nodes are available, **it may still schedule the pod on `worker-node-2`**.

#### **Step 3: Apply the Pod YAML**
```sh
kubectl apply -f prefernoschedule-pod.yaml
```

#### **Step 4: Remove the `PreferNoSchedule` Taint (If Needed)**
```sh
kubectl taint nodes worker-node-2 key=value:PreferNoSchedule-
```
This removes the taint and allows normal scheduling.

---

### **3. `NoExecute` Taint with Example YAML**  

#### **Step 1: Taint a Node with `NoExecute`**
```sh
kubectl taint nodes worker-node-3 key=value:NoExecute
```
- This taint **immediately evicts** running pods **unless they have a matching toleration**.

#### **Step 2: Create a Pod with a `NoExecute` Toleration**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: noexecute-pod
spec:
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoExecute"
  containers:
  - name: nginx-container
    image: nginx
```
- This pod has a **matching toleration**, so it **will not be evicted** even if `worker-node-3` has the `NoExecute` taint.

#### **Step 3: Apply the Pod YAML**
```sh
kubectl apply -f noexecute-pod.yaml
```

#### **Step 4: Remove the `NoExecute` Taint (If Needed)**
```sh
kubectl taint nodes worker-node-3 key=value:NoExecute-
```
This removes the taint, allowing all pods to run normally on `worker-node-3`.

---

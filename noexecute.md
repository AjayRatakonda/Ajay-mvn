To **practice the `NoExecute` condition** in your **K3s cluster with a master and worker node**, follow these steps:  

---

## **Step 1: Verify Your Nodes**  
Run the following command on your **master node** to list all nodes:  
```sh
kubectl get nodes
```
Example output:
```
NAME             STATUS   ROLES                  AGE   VERSION
master-node      Ready    control-plane,master   30m   v1.31.5+k3s1
worker-node-1    Ready    <none>                 25m   v1.31.5+k3s1
```
- Your **worker node is `worker-node-1`**.  

---

## **Step 2: Taint the Worker Node with `NoExecute`**  
Now, taint the **worker node** (`worker-node-1`) with `NoExecute`:  
```sh
kubectl taint nodes worker-node-1 key=value:NoExecute
```
Check if the taint is applied:  
```sh
kubectl describe node worker-node-1 | grep Taint
```
Expected output:  
```
Taints: key=value:NoExecute
```
This means **any pod running on `worker-node-1` without a matching toleration will be evicted immediately**.

![image](https://github.com/user-attachments/assets/d74a31d5-50fa-4049-bbfa-0260c9cffdd0)

---

## **Step 3: Create a Pod Without a `NoExecute` Toleration**  
Create a file `noexecute-evicted-pod.yaml`:  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: evicted-pod
spec:
  nodeSelector:
    kubernetes.io/hostname: worker-node-1
  containers:
  - name: nginx-container
    image: nginx
```
- This pod **does not** have a `NoExecute` toleration.  
- It **must** run on `worker-node-1` because of the `nodeSelector`.  

Apply the pod:  
```sh
kubectl apply -f noexecute-evicted-pod.yaml
```
Check the pod status:  
```sh
kubectl get pods -o wide
```
Expected behavior:
- The pod will be scheduled on `worker-node-1`.
- It will be **evicted immediately** because it does not have a matching toleration.
- The status will change to **`Evicted`**.

---

## **Step 4: Create a Pod That Tolerates `NoExecute`**  
To allow a pod to **stay on the node** despite the `NoExecute` taint, add a toleration.  

Create a file `noexecute-toleration-pod.yaml`:  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: tolerated-pod
spec:
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoExecute"
  nodeSelector:
    kubernetes.io/hostname: worker-node-1
  containers:
  - name: nginx-container
    image: nginx
```
- This pod **has a matching toleration** for the `NoExecute` taint.  
- It will **not be evicted** and will run normally on `worker-node-1`.  

Apply the pod:  
```sh
kubectl apply -f noexecute-toleration-pod.yaml
```
Check if it is running:  
```sh
kubectl get pods -o wide
```
Expected behavior:
- This pod will **stay running** on `worker-node-1` even though the node is tainted.  

---

## **Step 5: Remove the `NoExecute` Taint (If Needed)**  
If you want to remove the taint and allow normal scheduling:  
```sh
kubectl taint nodes worker-node-1 key=value:NoExecute-
```
Verify removal:  
```sh
kubectl describe node worker-node-1 | grep Taint
```
If no output appears, the taint is removed.

---

## **Summary of the Experiment**
1. **Taint `worker-node-1` with `NoExecute`** → Existing pods without toleration get evicted.  
2. **Create a pod without a `NoExecute` toleration** → It gets **evicted immediately**.  
3. **Create a pod with a `NoExecute` toleration** → It runs successfully.  
4. **Remove the taint** → Normal scheduling resumes.  

This will help you **practice and understand `NoExecute` in Kubernetes**. Let me know if you have any issues.

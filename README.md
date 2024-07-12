# K8s_Requests_Limits

Requests: This is the minimum amount of resources a pod needs to operate smoothly. Think of it as a guaranteed reservation for the pod.

Limits: This is the maximum amount of resources a pod can use. It acts as a safety cap to prevent any pod from consuming more than its fair share and disrupting others.

### Why?

- Resource Control: By setting limits, you prevent a single pod from monopolizing resources, which can lead to issues like out-of-memory (OOM) kills or CPU starvation. 
- Predictability: Requests help the scheduler allocate resources efficiently and ensure pods have the necessary resources to run effectively.

---

`kind create cluster --name=pavantest` 

`k apply -f metrics.yaml`

`k get pods -n kube-system`

![image](https://github.com/user-attachments/assets/78eac8c9-a61a-467b-aeb0-4c4b0e0c59ff)

`k top node`

![image](https://github.com/user-attachments/assets/7396fa70-e295-4d16-beeb-b1b00c7f85cf)

`k create ns mem-example`

## CASE - 1

`vi mem-stress.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo-2
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-2-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "50Mi"
      limits:
        memory: "300Mi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "250M", "--vm-hang", "1"]
```
`k apply -f mem-stress.yaml`

`k get pods -n mem-example -w`

![image](https://github.com/user-attachments/assets/994dce2e-9080-4d71-973b-d67424ede3ca)

`k top pod memory-demo-2 -n mem-example`
 
![image](https://github.com/user-attachments/assets/c4646ef1-5624-4fb6-97e0-c328c3dfb308)

## CASE - 2

Change the Memory Limit 150

```
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo-2
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-2-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "50Mi"
      limits:
        memory: "150Mi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "250M", "--vm-hang", "1"]
```

`k apply -f mem-stress.yaml`

`k get pods -n mem-example -w`

![image](https://github.com/user-attachments/assets/a5769288-b1ef-4b14-96df-b069a7ee06d8)

`k top pod memory-demo-2 -n mem-example`
 
 ![image](https://github.com/user-attachments/assets/424eedfb-e7a6-450b-8599-662b086d556c)
 
 (No pod running so no metrics)

 ## CASE - 3

 Set the Limits of Memory more than the Node

 ```
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo-2
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-2-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "1000Gi"
      limits:
        memory: "1000Gi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "250M", "--vm-hang", "1"]
```

`k apply -f mem-stress.yaml`

`k get pods -n mem-example -w`

![image](https://github.com/user-attachments/assets/d521dd3e-1417-44a8-8aef-c4d16ade6847)

`k describe pomemory-demo-2 -n mem-example`

![image](https://github.com/user-attachments/assets/c3c5d0d8-23e6-44ba-9858-1ee9f1d3067c)

`k top pod memory-demo-2 -n mem-example`
 
 ![image](https://github.com/user-attachments/assets/424eedfb-e7a6-450b-8599-662b086d556c)
 
 (No pod running so no metrics)

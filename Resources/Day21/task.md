## Task 21/40

In this exercise, you to work with TLS certificates in Kubernetes

### Task details
1. Generate a PKI private key and CSR and name it as learner.key and learner.csr
2. Create a CertificateSigningRequest for learner and set the expiration date to 1 week
3. Make sure to use the encoded value of csr in the request field
4. Approve the csr
5. Retrieve the certificate from the CSR
6. Export the issued certificate from the CertificateSigningRequest to a yaml
7. Redirect the certificate value to learner.crt file after decoding it
8. Verify the steps one more time, we will use these details in the next task.

```
controlplane:~$ openssl genrsa -out learner.key 2048
controlplane:~$ openssl req -new -key learner.key -out learner.csr -subj "/CN=adam"
controlplane:~$ ls
adam.csr  adam.key  filesystem  learner.csr  learner.key
controlplane:~$ cat learner.csr | base64 | tr -d '\n'
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VZV1JoYlRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUxuQUM5aDF5MUx4bWplVWlxRzJ1MVZPSmRVSmJQdzJnenZnVEdnN0hwNjhoaEpBCk5tK2dvTk9YQk9xWmxsbWxWUGNnNlREak5sZlNVdlV6VzdzdnRYeEtFeEt1dWs0SWRDcDBGeXFXVTFNZTNtRWwKbUJSL3I2ZzNiMXJTSlg2ODVndndrU2ZaNEtLT1R5SzRKdnEwZnYxbWhUdGdDeWxnd1RqNEpGNXYxdHZzdlFLMgpMUGxzT3F5N2FOcm93Q01KZis5QjY4TjlMbzc5U1E1Uk1IWFdkSTRsdi9CSEhvRGc2UEsxdlE4ZmQybVJyb3JzCmo0WGlKdXM4UDV0QkFpUDBER2xnL1BPQTNWTXkxdlp4b0RxbmoydWREQW9PY2l2OFo2M0VSSGhkQmNwaE8zN0gKblJiYUI3aWh2SWx4Wk45N1dpemNOKzFTMXN0TXhObUVkZ0lVbjlVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQmQ1aHVJaWlQelY2SDhweFpaOWI4bUV2eG13bEc3bUpXTWNRMk52cEhUWkNHc1hackV2SExCCjdVTW5zcEZJekhSR1RVWTdYQ2RMWEJsaVEvL0RsbXlETEVNZkFxc3hkZk5CZGhHM0d5aGlNMjlqSUdKcGlaWisKWFRldzhqbEdURjYxRklHK3ZaSEZtRXBFdGI4SUwwRmFQRlRxcHJtL3pFT2JHVHpZRFQyRk1FWjVSQ0duZGNUaApuK0pGcjBXMDdHMVduRmt6OEUzZWFqeUUydkxkSnpxbWlCWHI4b2trWk5vVzZzSXpxby9VYUNUUjE0V3BjdUZYCjl6UVNabDd6djluNm9DR1BhZWV6cXo2Unk1QnIxY2R1Znp3SVQ3ZTZLZDIyNWxoaGhTMStNMFNteHl4VnNtR04KV1hDTjd3aTNncjh6aG9Wdm16RXZ2bjlZNkpFT2VrdXEKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==controlplane:~$ 
controlplane:~$ 
controlplane:~$ vi csr-manifest.yaml
controlplane:~$ vi csr-manifest.yaml
controlplane:~$ kubectl apply -f csr-manifest.yaml 
error: error parsing csr-manifest.yaml: error converting YAML to JSON: yaml: line 6: mapping values are not allowed in this context
controlplane:~$ cat csr-manifest.yaml 
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: learner-user-csr
spec:
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VZV1JoYlRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUxuQUM5aDF5MUx4bWplVWlxRzJ1MVZPSmRVSmJQdzJnenZnVEdnN0hwNjhoaEpBCk5tK2dvTk9YQk9xWmxsbWxWUGNnNlREak5sZlNVdlV6VzdzdnRYeEtFeEt1dWs0SWRDcDBGeXFXVTFNZTNtRWwKbUJSL3I2ZzNiMXJTSlg2ODVndndrU2ZaNEtLT1R5SzRKdnEwZnYxbWhUdGdDeWxnd1RqNEpGNXYxdHZzdlFLMgpMUGxzT3F5N2FOcm93Q01KZis5QjY4TjlMbzc5U1E1Uk1IWFdkSTRsdi9CSEhvRGc2UEsxdlE4ZmQybVJyb3JzCmo0WGlKdXM4UDV0QkFpUDBER2xnL1BPQTNWTXkxdlp4b0RxbmoydWREQW9PY2l2OFo2M0VSSGhkQmNwaE8zN0gKblJiYUI3aWh2SWx4Wk45N1dpemNOKzFTMXN0TXhObUVkZ0lVbjlVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQmQ1aHVJaWlQelY2SDhweFpaOWI4bUV2eG13bEc3bUpXTWNRMk52cEhUWkNHc1hackV2SExCCjdVTW5zcEZJekhSR1RVWTdYQ2RMWEJsaVEvL0RsbXlETEVNZkFxc3hkZk5CZGhHM0d5aGlNMjlqSUdKcGlaWisKWFRldzhqbEdURjYxRklHK3ZaSEZtRXBFdGI4SUwwRmFQRlRxcHJtL3pFT2JHVHpZRFQyRk1FWjVSQ0duZGNUaApuK0pGcjBXMDdHMVduRmt6OEUzZWFqeUUydkxkSnpxbWlCWHI4b2trWk5vVzZzSXpxby9VYUNUUjE0V3BjdUZYCjl6UVNabDd6djluNm9DR1BhZWV6cXo2Unk1QnIxY2R1Znp3SVQ3ZTZLZDIyNWxoaGhTMStNMFNteHl4VnNtR04KV1hDTjd3aTNncjh6aG9Wdm16RXZ2bjlZNkpFT2VrdXEKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 604800   # 1 day
  usages:
    - client auth

controlplane:~$ vi csr-manifest.yaml
controlplane:~$ kubectl apply -f csr-manifest.yaml 
certificatesigningrequest.certificates.k8s.io/learner-user-csr created
controlplane:~$ kubectl get csr 
NAME               AGE   SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
csr-lcvcl          11d   kubernetes.io/kube-apiserver-client-kubelet   system:bootstrap:m3nvn0    <none>              Approved,Issued
csr-v4sgd          11d   kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued
learner-user-csr   11s   kubernetes.io/kube-apiserver-client           kubernetes-admin           7d                  Pending
controlplane:~$ kubectl describe csr learner-user-csr 
Name:         learner-user-csr
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"certificates.k8s.io/v1","kind":"CertificateSigningRequest","metadata":{"annotations":{},"name":"learner-user-csr"},"spec":{"expirationSeconds":604800,"request":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VZV1JoYlRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUxuQUM5aDF5MUx4bWplVWlxRzJ1MVZPSmRVSmJQdzJnenZnVEdnN0hwNjhoaEpBCk5tK2dvTk9YQk9xWmxsbWxWUGNnNlREak5sZlNVdlV6VzdzdnRYeEtFeEt1dWs0SWRDcDBGeXFXVTFNZTNtRWwKbUJSL3I2ZzNiMXJTSlg2ODVndndrU2ZaNEtLT1R5SzRKdnEwZnYxbWhUdGdDeWxnd1RqNEpGNXYxdHZzdlFLMgpMUGxzT3F5N2FOcm93Q01KZis5QjY4TjlMbzc5U1E1Uk1IWFdkSTRsdi9CSEhvRGc2UEsxdlE4ZmQybVJyb3JzCmo0WGlKdXM4UDV0QkFpUDBER2xnL1BPQTNWTXkxdlp4b0RxbmoydWREQW9PY2l2OFo2M0VSSGhkQmNwaE8zN0gKblJiYUI3aWh2SWx4Wk45N1dpemNOKzFTMXN0TXhObUVkZ0lVbjlVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQmQ1aHVJaWlQelY2SDhweFpaOWI4bUV2eG13bEc3bUpXTWNRMk52cEhUWkNHc1hackV2SExCCjdVTW5zcEZJekhSR1RVWTdYQ2RMWEJsaVEvL0RsbXlETEVNZkFxc3hkZk5CZGhHM0d5aGlNMjlqSUdKcGlaWisKWFRldzhqbEdURjYxRklHK3ZaSEZtRXBFdGI4SUwwRmFQRlRxcHJtL3pFT2JHVHpZRFQyRk1FWjVSQ0duZGNUaApuK0pGcjBXMDdHMVduRmt6OEUzZWFqeUUydkxkSnpxbWlCWHI4b2trWk5vVzZzSXpxby9VYUNUUjE0V3BjdUZYCjl6UVNabDd6djluNm9DR1BhZWV6cXo2Unk1QnIxY2R1Znp3SVQ3ZTZLZDIyNWxoaGhTMStNMFNteHl4VnNtR04KV1hDTjd3aTNncjh6aG9Wdm16RXZ2bjlZNkpFT2VrdXEKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==","signerName":"kubernetes.io/kube-apiserver-client","usages":["client auth"]}}

CreationTimestamp:   Tue, 30 Dec 2025 13:19:34 +0000
Requesting User:     kubernetes-admin
Signer:              kubernetes.io/kube-apiserver-client
Requested Duration:  7d
Status:              Pending
Subject:
         Common Name:    adam
         Serial Number:  
Events:  <none>
controlplane:~$ kubectl certificate approve learner-user-csr
certificatesigningrequest.certificates.k8s.io/learner-user-csr approved
controlplane:~$ kubectl get csr 
NAME               AGE   SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
csr-lcvcl          11d   kubernetes.io/kube-apiserver-client-kubelet   system:bootstrap:m3nvn0    <none>              Approved,Issued
csr-v4sgd          11d   kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued
learner-user-csr   53s   kubernetes.io/kube-apiserver-client           kubernetes-admin           7d                  Approved,Issued
controlplane:~$ kubectl describe csr learner-user-csr 
Name:         learner-user-csr
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"certificates.k8s.io/v1","kind":"CertificateSigningRequest","metadata":{"annotations":{},"name":"learner-user-csr"},"spec":{"expirationSeconds":604800,"request":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VZV1JoYlRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUxuQUM5aDF5MUx4bWplVWlxRzJ1MVZPSmRVSmJQdzJnenZnVEdnN0hwNjhoaEpBCk5tK2dvTk9YQk9xWmxsbWxWUGNnNlREak5sZlNVdlV6VzdzdnRYeEtFeEt1dWs0SWRDcDBGeXFXVTFNZTNtRWwKbUJSL3I2ZzNiMXJTSlg2ODVndndrU2ZaNEtLT1R5SzRKdnEwZnYxbWhUdGdDeWxnd1RqNEpGNXYxdHZzdlFLMgpMUGxzT3F5N2FOcm93Q01KZis5QjY4TjlMbzc5U1E1Uk1IWFdkSTRsdi9CSEhvRGc2UEsxdlE4ZmQybVJyb3JzCmo0WGlKdXM4UDV0QkFpUDBER2xnL1BPQTNWTXkxdlp4b0RxbmoydWREQW9PY2l2OFo2M0VSSGhkQmNwaE8zN0gKblJiYUI3aWh2SWx4Wk45N1dpemNOKzFTMXN0TXhObUVkZ0lVbjlVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQmQ1aHVJaWlQelY2SDhweFpaOWI4bUV2eG13bEc3bUpXTWNRMk52cEhUWkNHc1hackV2SExCCjdVTW5zcEZJekhSR1RVWTdYQ2RMWEJsaVEvL0RsbXlETEVNZkFxc3hkZk5CZGhHM0d5aGlNMjlqSUdKcGlaWisKWFRldzhqbEdURjYxRklHK3ZaSEZtRXBFdGI4SUwwRmFQRlRxcHJtL3pFT2JHVHpZRFQyRk1FWjVSQ0duZGNUaApuK0pGcjBXMDdHMVduRmt6OEUzZWFqeUUydkxkSnpxbWlCWHI4b2trWk5vVzZzSXpxby9VYUNUUjE0V3BjdUZYCjl6UVNabDd6djluNm9DR1BhZWV6cXo2Unk1QnIxY2R1Znp3SVQ3ZTZLZDIyNWxoaGhTMStNMFNteHl4VnNtR04KV1hDTjd3aTNncjh6aG9Wdm16RXZ2bjlZNkpFT2VrdXEKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==","signerName":"kubernetes.io/kube-apiserver-client","usages":["client auth"]}}

CreationTimestamp:   Tue, 30 Dec 2025 13:19:34 +0000
Requesting User:     kubernetes-admin
Signer:              kubernetes.io/kube-apiserver-client
Requested Duration:  7d
Status:              Approved,Issued
Subject:
         Common Name:    adam
         Serial Number:  
Events:  <none>
controlplane:~$ echo "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VZV1JoYlRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUxuQUM5aDF5MUx4bWplVWlxRzJ1MVZPSmRVSmJQdzJnenZnVEdnN0hwNjhoaEpBCk5tK2dvTk9YQk9xWmxsbWxWUGNnNlREak5sZlNVdlV6VzdzdnRYeEtFeEt1dWs0SWRDcDBGeXFXVTFNZTNtRWwKbUJSL3I2ZzNiMXJTSlg2ODVndndrU2ZaNEtLT1R5SzRKdnEwZnYxbWhUdGdDeWxnd1RqNEpGNXYxdHZzdlFLMgpMUGxzT3F5N2FOcm93Q01KZis5QjY4TjlMbzc5U1E1Uk1IWFdkSTRsdi9CSEhvRGc2UEsxdlE4ZmQybVJyb3JzCmo0WGlKdXM4UDV0QkFpUDBER2xnL1BPQTNWTXkxdlp4b0RxbmoydWREQW9PY2l2OFo2M0VSSGhkQmNwaE8zN0gKblJiYUI3aWh2SWx4Wk45N1dpemNOKzFTMXN0TXhObUVkZ0lVbjlVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQmQ1aHVJaWlQelY2SDhweFpaOWI4bUV2eG13bEc3bUpXTWNRMk52cEhUWkNHc1hackV2SExCCjdVTW5zcEZJekhSR1RVWTdYQ2RMWEJsaVEvL0RsbXlETEVNZkFxc3hkZk5CZGhHM0d5aGlNMjlqSUdKcGlaWisKWFRldzhqbEdURjYxRklHK3ZaSEZtRXBFdGI4SUwwRmFQRlRxcHJtL3pFT2JHVHpZRFQyRk1FWjVSQ0duZGNUaApuK0pGcjBXMDdHMVduRmt6OEUzZWFqeUUydkxkSnpxbWlCWHI4b2trWk5vVzZzSXpxby9VYUNUUjE0V3BjdUZYCjl6UVNabDd6djluNm9DR1BhZWV6cXo2Unk1QnIxY2R1Znp3SVQ3ZTZLZDIyNWxoaGhTMStNMFNteHl4VnNtR04KV1hDTjd3aTNncjh6aG9Wdm16RXZ2bjlZNkpFT2VrdXEKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==" | base64 -d
-----BEGIN CERTIFICATE REQUEST-----
MIICVDCCATwCAQAwDzENMAsGA1UEAwwEYWRhbTCCASIwDQYJKoZIhvcNAQEBBQAD
ggEPADCCAQoCggEBALnAC9h1y1LxmjeUiqG2u1VOJdUJbPw2gzvgTGg7Hp68hhJA
Nm+goNOXBOqZllmlVPcg6TDjNlfSUvUzW7svtXxKExKuuk4IdCp0FyqWU1Me3mEl
mBR/r6g3b1rSJX685gvwkSfZ4KKOTyK4Jvq0fv1mhTtgCylgwTj4JF5v1tvsvQK2
LPlsOqy7aNrowCMJf+9B68N9Lo79SQ5RMHXWdI4lv/BHHoDg6PK1vQ8fd2mRrors
j4XiJus8P5tBAiP0DGlg/POA3VMy1vZxoDqnj2udDAoOciv8Z63ERHhdBcphO37H
nRbaB7ihvIlxZN97WizcN+1S1stMxNmEdgIUn9UCAwEAAaAAMA0GCSqGSIb3DQEB
CwUAA4IBAQBd5huIiiPzV6H8pxZZ9b8mEvxmwlG7mJWMcQ2NvpHTZCGsXZrEvHLB
7UMnspFIzHRGTUY7XCdLXBliQ//DlmyDLEMfAqsxdfNBdhG3GyhiM29jIGJpiZZ+
XTew8jlGTF61FIG+vZHFmEpEtb8IL0FaPFTqprm/zEObGTzYDT2FMEZ5RCGndcTh
n+JFr0W07G1WnFkz8E3eajyE2vLdJzqmiBXr8okkZNoW6sIzqo/UaCTR14WpcuFX
9zQSZl7zv9n6oCGPaeezqz6Ry5Br1cdufzwIT7e6Kd225lhhhS1+M0SmxyxVsmGN
WXCN7wi3gr8zhoVvmzEvvn9Y6JEOekuq
-----END CERTIFICATE REQUEST-----
controlplane:~$ echo "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VZV1JoYlRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUxuQUM5aDF5MUx4bWplVWlxRzJ1MVZPSmRVSmJQdzJnenZnVEdnN0hwNjhoaEpBCk5tK2dvTk9YQk9xWmxsbWxWUGNnNlREak5sZlNVdlV6VzdzdnRYeEtFeEt1dWs0SWRDcDBGeXFXVTFNZTNtRWwKbUJSL3I2ZzNiMXJTSlg2ODVndndrU2ZaNEtLT1R5SzRKdnEwZnYxbWhUdGdDeWxnd1RqNEpGNXYxdHZzdlFLMgpMUGxzT3F5N2FOcm93Q01KZis5QjY4TjlMbzc5U1E1Uk1IWFdkSTRsdi9CSEhvRGc2UEsxdlE4ZmQybVJyb3JzCmo0WGlKdXM4UDV0QkFpUDBER2xnL1BPQTNWTXkxdlp4b0RxbmoydWREQW9PY2l2OFo2M0VSSGhkQmNwaE8zN0gKblJiYUI3aWh2SWx4Wk45N1dpemNOKzFTMXN0TXhObUVkZ0lVbjlVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQmQ1aHVJaWlQelY2SDhweFpaOWI4bUV2eG13bEc3bUpXTWNRMk52cEhUWkNHc1hackV2SExCCjdVTW5zcEZJekhSR1RVWTdYQ2RMWEJsaVEvL0RsbXlETEVNZkFxc3hkZk5CZGhHM0d5aGlNMjlqSUdKcGlaWisKWFRldzhqbEdURjYxRklHK3ZaSEZtRXBFdGI4SUwwRmFQRlRxcHJtL3pFT2JHVHpZRFQyRk1FWjVSQ0duZGNUaApuK0pGcjBXMDdHMVduRmt6OEUzZWFqeUUydkxkSnpxbWlCWHI4b2trWk5vVzZzSXpxby9VYUNUUjE0V3BjdUZYCjl6UVNabDd6djluNm9DR1BhZWV6cXo2Unk1QnIxY2R1Znp3SVQ3ZTZLZDIyNWxoaGhTMStNMFNteHl4VnNtR04KV1hDTjd3aTNncjh6aG9Wdm16RXZ2bjlZNkpFT2VrdXEKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==" | base64 -d > learner.crt
controlplane:~$ cat learner.
cat: learner.: No such file or directory
controlplane:~$ cat learner.crt 
-----BEGIN CERTIFICATE REQUEST-----
MIICVDCCATwCAQAwDzENMAsGA1UEAwwEYWRhbTCCASIwDQYJKoZIhvcNAQEBBQAD
ggEPADCCAQoCggEBALnAC9h1y1LxmjeUiqG2u1VOJdUJbPw2gzvgTGg7Hp68hhJA
Nm+goNOXBOqZllmlVPcg6TDjNlfSUvUzW7svtXxKExKuuk4IdCp0FyqWU1Me3mEl
mBR/r6g3b1rSJX685gvwkSfZ4KKOTyK4Jvq0fv1mhTtgCylgwTj4JF5v1tvsvQK2
LPlsOqy7aNrowCMJf+9B68N9Lo79SQ5RMHXWdI4lv/BHHoDg6PK1vQ8fd2mRrors
j4XiJus8P5tBAiP0DGlg/POA3VMy1vZxoDqnj2udDAoOciv8Z63ERHhdBcphO37H
nRbaB7ihvIlxZN97WizcN+1S1stMxNmEdgIUn9UCAwEAAaAAMA0GCSqGSIb3DQEB
CwUAA4IBAQBd5huIiiPzV6H8pxZZ9b8mEvxmwlG7mJWMcQ2NvpHTZCGsXZrEvHLB
7UMnspFIzHRGTUY7XCdLXBliQ//DlmyDLEMfAqsxdfNBdhG3GyhiM29jIGJpiZZ+
XTew8jlGTF61FIG+vZHFmEpEtb8IL0FaPFTqprm/zEObGTzYDT2FMEZ5RCGndcTh
n+JFr0W07G1WnFkz8E3eajyE2vLdJzqmiBXr8okkZNoW6sIzqo/UaCTR14WpcuFX
9zQSZl7zv9n6oCGPaeezqz6Ry5Br1cdufzwIT7e6Kd225lhhhS1+M0SmxyxVsmGN
WXCN7wi3gr8zhoVvmzEvvn9Y6JEOekuq
-----END CERTIFICATE REQUEST-----
controlplane:~$

```
   
10. **Share your learnings**: As this is a sub-task, it will be better if you create the blog of day21 and day22 all together.
11. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.

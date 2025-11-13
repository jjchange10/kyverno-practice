Kyverno インストール
```
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
helm install kyverno kyverno/kyverno -n kyverno --create-namespace
```


quick-start
- clusterpolicyを設定
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-labels
spec:
  rules:
  - name: check-team
    match:
      any:
      - resources:
          kinds:
          - Pod
    validate:
      failureAction: Enforce
      message: "label 'team' is required"
      pattern:
        metadata:
          labels:
            team: "?*"
```
- nginxのPodを作成(異常系)
```
kubectl create deployment nginx --image=nginx
error: failed to create deployment: admission webhook "validate.kyverno.svc-fail" denied the request: 

resource Deployment/default/nginx was blocked due to the following policies 

require-labels:
  autogen-check-team: 'validation error: label ''team'' is required. rule autogen-check-team
    failed at path /spec/template/metadata/labels/team/'
```

- nginxのPodを作成(正常系)
```
kubectl run nginx --image nginx --labels team=backend
pod/nginx created
```

- reportを確認
```
kubectl get policyreport -A -o wide
NAMESPACE            NAME                                   KIND         NAME                                                    PASS   FAIL   WARN   ERROR   SKIP   AGE
default              f723263c-ae8c-4fc2-aa63-8a24bdc0077b   Pod          nginx                                                   1      0      0      0       0      4s
kube-system          1474b64f-b66f-4f5e-8f96-a42d5c8c59b2   Pod          kube-apiserver-kyverno-cluster-control-plane            0      1      0      0       0      70s
kube-system          16153be0-3e6f-4372-aca8-6442e940a2d9   Pod          etcd-kyverno-cluster-control-plane                      0      1      0      0       0      70s
kube-system          31a794f3-96eb-4331-8b09-b4123d956e11   ReplicaSet   coredns-66bc5c9577                                      0      1      0      0       0      70s
kube-system          3f8bef18-e35e-4cde-bb88-3dc67808a267   DaemonSet    kindnet                                                 0      1      0      0       0      70s
kube-system          429e3370-0145-4a7a-9a57-5290085d9678   DaemonSet    kube-proxy                                              0      1      0      0       0      70s
kube-system          4ba4856f-ffa9-4db8-8f21-d2da34aa781c   Pod          kindnet-8gfnl                                           0      1      0      0       0      70s
kube-system          679924db-a185-4405-b4ab-a4503b9004a8   Pod          kube-proxy-sjfj2                                        0      1      0      0       0      70s
kube-system          6c8eafb3-6d94-4c77-ad54-39db1dadfda9   Pod          kindnet-q7g8x                                           0      1      0      0       0      70s
kube-system          6cee8e86-47ef-4ccf-9b02-a91e127e7fad   Deployment   coredns                                                 0      1      0      0       0      70s
kube-system          72451aeb-994b-4866-9879-69e63e25c734   Pod          coredns-66bc5c9577-pqtmx                                0      1      0      0       0      70s
kube-system          84941f4e-b9f8-4eab-ad92-5de5c2c2a002   Pod          kube-proxy-fc8h2                                        0      1      0      0       0      70s
kube-system          8948bb31-19af-4ff2-aae3-b14f9598855d   Pod          kindnet-n59qf                                           0      1      0      0       0      70s
kube-system          8f22d1a0-acf9-4008-abfc-81d626eaaa48   Pod          kube-controller-manager-kyverno-cluster-control-plane   0      1      0      0       0      70s
kube-system          c34327cb-6ca3-4bfd-983a-3ca7945f8389   Pod          kube-scheduler-kyverno-cluster-control-plane            0      1      0      0       0      70s
kube-system          d09acaa1-bb4c-4b85-955e-d3d8a88e54fb   Pod          coredns-66bc5c9577-qtt6w                                0      1      0      0       0      70s
kube-system          fee4b7c9-5cef-4c3a-a4f5-ec05062a9217   Pod          kube-proxy-tqpcm                                        0      1      0      0       0      70s
kyverno              0b1092cd-b0dd-4cba-ab5c-b0d081dd9984   ReplicaSet   kyverno-background-controller-bf87c98c                  0      1      0      0       0      70s
kyverno              25badb9a-beb2-45cd-a424-772e838aa730   ReplicaSet   kyverno-admission-controller-686f5b7fcf                 0      1      0      0       0      70s
kyverno              345687bf-4368-4d59-82ee-a108190366f8   Pod          kyverno-cleanup-controller-78646569b6-x4j8p             0      1      0      0       0      70s
kyverno              37951ba7-b935-4413-8273-4c198debafb0   Deployment   kyverno-cleanup-controller                              0      1      0      0       0      70s
kyverno              6bf2e05c-edc2-4bc9-a35c-24234189c509   Pod          kyverno-background-controller-bf87c98c-5vq8f            0      1      0      0       0      70s
kyverno              712182c6-a5dc-42d3-8f9d-fbcc56260879   Pod          kyverno-reports-controller-5d5cc4f8d6-pmxlt             0      1      0      0       0      70s
kyverno              8411638b-18fa-414a-8ee7-b1c6dc5d21e9   Pod          kyverno-admission-controller-686f5b7fcf-7cnfx           0      1      0      0       0      70s
kyverno              8abf0a63-7cde-442b-9af3-cb4a25993e14   Deployment   kyverno-reports-controller                              0      1      0      0       0      70s
kyverno              8efad047-3fe7-4e52-b8e7-c706d9f95cdd   ReplicaSet   kyverno-cleanup-controller-78646569b6                   0      1      0      0       0      70s
kyverno              989de1e2-2b9f-4337-8ae6-69d78dcc2d80   Deployment   kyverno-background-controller                           0      1      0      0       0      70s
kyverno              9d1037a2-5d8e-4326-b385-ea2699647a73   ReplicaSet   kyverno-reports-controller-5d5cc4f8d6                   0      1      0      0       0      70s
kyverno              cc7c06c2-005e-4e1e-b210-86a488af80fd   Deployment   kyverno-admission-controller                            0      1      0      0       0      70s
local-path-storage   50fdc1c7-5b58-49f5-9da3-e8729b9dc1a7   Deployment   local-path-provisioner                                  0      1      0      0       0      70s
local-path-storage   b0cd7e02-32fd-4e54-9680-41b2db4549a4   ReplicaSet   local-path-provisioner-7b8c8ddbd6                       0      1      0      0       0      70s
local-path-storage   bf47ee87-77a0-4f3d-a9fb-0cc1f6a1dc4a   Pod          local-path-provisioner-7b8c8ddbd6-ctdmj                 0      1      0      0       0      70s
```


## 証明書確認(デフォルト１年)
Kyvernoは証明書を生成および管理するので、自動でローテーションされる。１２時間ごとに有効性が確認され、15日前に更新される。
```
kubectl -n kyverno get secret kyverno-svc.kyverno.svc.kyverno-tls-ca -o jsonpath='{.data.tls\.crt}' | base64 -d | openssl x509 -text -noout
```

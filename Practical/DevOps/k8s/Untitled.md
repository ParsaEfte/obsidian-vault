# جزوه جامع کامندهای کوبرنتیز (Kubernetes)

---

## 1) بررسی نسخه و ارتباط با کلاستر

### مشاهدهٔ نسخهٔ Client و Server (Control Plane)

```bash
kubectl version --output=yaml
```

نسخهٔ خلاصه و تمیز:

```bash
kubectl version --client
```

---

## 2) تنظیمات محیط و میان‌برها (Autocomplete & Aliases)

### فعال‌سازی Auto-completion در Bash

```bash
source <(kubectl completion bash)
```

### اضافه کردن دائمی تکمیل خودکار و Alias محبوب `k` به `.bashrc`

```bash
echo "source <(kubectl completion bash)" >> ~/.bashrc
echo "alias k=kubectl" >> ~/.bashrc
echo "complete -o default -F __start_kubectl k" >> ~/.bashrc
source ~/.bashrc
```

استفاده:

```bash
k get pods
k get nodes
```

---

## 3) مدیریت Context و Kubeconfig

### مشاهدهٔ تنظیمات فعلی Kubeconfig

```bash
kubectl config view
```

مشاهدهٔ Kubeconfig همراه با مقادیر خام (مانند داده‌های رمزگذاری‌شده و گواهی‌ها):

```bash
kubectl config view --raw
```

### مشاهدهٔ کانتکست فعال فعلی

```bash
kubectl config current-context
```

### لیست تمام کانتکست‌های موجود

```bash
kubectl config get-contexts
```

### سوئیچ کردن بین کانتکست‌ها (کلاسترها)

```bash
kubectl config use-context <context-name>
```

مثال:

```bash
kubectl config use-context kubernetes-admin@kubernetes
```

### تعیین Namespace پیش‌فرض برای کانتکست فعال

```bash
kubectl config set-context --current --namespace=<namespace-name>
```

مثال:

```bash
kubectl config set-context --current --namespace=production
```

### بررسی اطلاعات عمومی و سلامت کامپوننت‌های کلاستر

```bash
kubectl cluster-info
```

نمایش اطلاعات جامع کلاستر جهت عیب‌یابی:

```bash
kubectl cluster-info dump
```

---

## 4) کار با Namespaceها

### لیست تمام Namespaceها

```bash
kubectl get namespaces
# یا اختصار:
kubectl get ns
```

### ساخت یک Namespace جدید

```bash
kubectl create namespace staging
```

### ساخت مانیفست YAML یک Namespace به‌صورت خودکار

```bash
kubectl create namespace staging --dry-run=client -o yaml > namespace.yaml
```

### حذف یک Namespace (همراه با تمام منابع داخل آن)

```bash
kubectl delete namespace staging
```

> ⚠️ حذف Namespace باعث نابودی تمام پادها، سرویس‌ها، سکرت‌ها و دیتای محلی درون آن Namespace می‌شود.

---

## 5) مدیریت و نظارت بر نودها (Nodes)

### مشاهدهٔ وضعیت نودها

```bash
kubectl get nodes
```

مشاهدهٔ نودها با اطلاعات تکمیلی (IP داخلی، IP خارجی، سیستم‌عامل، نگارش Kernel و Container Runtime):

```bash
kubectl get nodes -o wide
```

### مشاهدهٔ جزئیات کامل و رویدادهای یک نود

```bash
kubectl describe node <node-name>
```

مثال:

```bash
kubectl describe node control-1
```

### برچسب‌گذاری (Labeling) نودها

افزودن برچسب:

```bash
kubectl label nodes <node-name> node-role.kubernetes.io/worker=worker
kubectl label nodes <node-name> disktype=ssd
```

حذف برچسب:

```bash
kubectl label nodes <node-name> disktype-
```

مشاهدهٔ برچسب‌های نودها:

```bash
kubectl get nodes --show-labels
```

### مدیریت زمان‌بندی و تخلیهٔ نود (Maintenance)

غیرفعال‌کردن زمان‌بندی روی نود (جلوگیری از ایجاد پادهای جدید):

```bash
kubectl cordon <node-name>
```

فعال‌سازی مجدد زمان‌بندی روی نود:

```bash
kubectl uncordon <node-name>
```

تخلیهٔ کامل پادها از روی نود جهت ارتقا یا تعمیرات:

```bash
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
```

### مدیریت Taints و Tolerations روی نود

اعمال Taint روی یک نود:

```bash
kubectl taint nodes <node-name> dedicated=infra:NoSchedule
```

حذف Taint از نود:

```bash
kubectl taint nodes <node-name> dedicated=infra:NoSchedule-
```

---

## 6) مدیریت Podها

### اجرای سریع یک پاد (دستوری / Imperative)

```bash
kubectl run nginx-pod --image=nginx:alpine --port=80
```

اجرای پاد همراه با متغیرهای محیطی:

```bash
kubectl run backend-pod --image=my-backend:v1 --env="APP_ENV=prod" --env="PORT=8080"
```

اجرای یک پاد موقت تستی و ورود مستقیم به شل آن (حذف خودکار پس از خروج):

```bash
kubectl run debug-shell --image=busybox -it --rm -- restart=Never -- sh
```

### تولید فایل YAML پاد بدون اجرای واقعی (Dry-run)

```bash
kubectl run my-pod --image=nginx --dry-run=client -o yaml > pod.yaml
```

### مشاهده و فیلتر پادها

مشاهدهٔ پادهای Namespace فعلی:

```bash
kubectl get pods
```

مشاهدهٔ پادها با IP و نود میزبان:

```bash
kubectl get pods -o wide
```

مشاهدهٔ پادها در تمام Namespaceها:

```bash
kubectl get pods -A
# یا:
kubectl get pods --all-namespaces
```

مشاهدهٔ لحظه‌ای و پیوستهٔ تغییر وضعیت پادها:

```bash
kubectl get pods -w
```

فیلتر پادها بر اساس برچسب (Label Selector):

```bash
kubectl get pods -l app=nginx
kubectl get pods -l 'env in (production, staging)'
```

### حذف پادها

حذف یک پاد مشخص:

```bash
kubectl delete pod nginx-pod
```

حذف اجباری و فوری یک پاد (Force Delete):

```bash
kubectl delete pod nginx-pod --grace-period=0 --force
```

حذف همهٔ پادهای دارای یک برچسب مشخص:

```bash
kubectl delete pods -l app=test
```

---

## 7) لاگ‌ها، مانیتورینگ و عیب‌یابی (Troubleshooting)

### بررسی مشخصات و خطاهای یک پاد

```bash
kubectl describe pod <pod-name>
```

### مشاهدهٔ لاگ‌های یک پاد

```bash
kubectl logs <pod-name>
```

دنبال‌کردن لاگ‌ها به‌صورت زنده (Real-time Follow):

```bash
kubectl logs -f <pod-name>
```

مشاهدهٔ ۱۰۰ خط آخر لاگ‌ها:

```bash
kubectl logs --tail=100 <pod-name>
```

مشاهدهٔ لاگ‌های یک کانتینر خاص در پادهای چندکانتینری:

```bash
kubectl logs <pod-name> -c <container-name>
```

مشاهدهٔ لاگ‌های نمونهٔ قبلی کانتینر که دچار کرش شده است (CrashLoopBackOff):

```bash
kubectl logs <pod-name> --previous
```

مشاهدهٔ لاگ تمام پادهای مرتبط با یک دیپلویمنت:

```bash
kubectl logs deployment/web-app --all-containers=true -f
```

### ورود به محیط شل داخل کانتینر (Exec)

```bash
kubectl exec -it <pod-name> -- /bin/sh
# یا در صورت وجود Bash:
kubectl exec -it <pod-name> -- /bin/bash
```

ورود به کانتینر خاص در پاد چندکانتینری:

```bash
kubectl exec -it <pod-name> -c <container-name> -- /bin/sh
```

اجرای یک تک‌دستور بدون لاگین کامل:

```bash
kubectl exec <pod-name> -- printenv
```

### کپی فایل بین کانتینر و سیستم محلی (Copy)

کپی فایل از لوکال به داخل پاد:

```bash
kubectl cp ./config.json <pod-name>:/app/config.json
```

کپی فایل از داخل پاد به سیستم لوکال:

```bash
kubectl cp <pod-name>:/app/data.log ./downloaded.log
```

### پورت‌فورواردینگ برای تست محلی (Port-Forward)

اتصال یک پورت از ماشین لوکال به پاد:

```bash
kubectl port-forward pod/<pod-name> 8080:80
```

اتصال مستقیم به سرویس یا دیپلویمنت:

```bash
kubectl port-forward svc/<service-name> 8080:80
kubectl port-forward deployment/<deployment-name> 8080:80
```

---

## 8) مدیریت Deploymentها و مقیاس‌پذیری (Scaling)

### ایجاد Deployment

ایجاد یک Deployment با ۳ رپلیکا:

```bash
kubectl create deployment web-app --image=nginx:alpine --replicas=3
```

تولید مانیفست YAML یک دیپلویمنت بدون اجرا:

```bash
kubectl create deployment web-app --image=nginx:alpine --replicas=3 --dry-run=client -o yaml > deployment.yaml
```

### مشاهدهٔ وضعیت Deploymentها

```bash
kubectl get deployments
# یا اختصار:
kubectl get deploy
```

مشاهدهٔ ReplicaSetهای ساخته‌شده توسط دیپلویمنت:

```bash
kubectl get replicasets
# یا اختصار:
kubectl get rs
```

### مقیاس‌پذیری دستی (Scale Up / Down)

تغییر تعداد کپی‌ها به ۵ عدد:

```bash
kubectl scale deployment web-app --replicas=5
```

### تنظیم مقیاس‌پذیری خودکار (Horizontal Pod Autoscaler - HPA)

تعیین حداقل و حداکثر تعداد پاد بر اساس مصرف CPU:

```bash
kubectl autoscale deployment web-app --min=2 --max=10 --cpu-percent=75
```

مشاهدهٔ وضعیت HPA:

```bash
kubectl get hpa
```

---

## 9) چرخهٔ به‌روزرسانی و Rollout

### آپدیت نسخهٔ Image دیپلویمنت

```bash
kubectl set image deployment/web-app nginx=nginx:1.25.0 --record
```

### مشاهدهٔ وضعیت پیشرفت Rollout

```bash
kubectl rollout status deployment/web-app
```

### مشاهدهٔ تاریخچهٔ بازبینی‌ها (Revision History)

```bash
kubectl rollout history deployment/web-app
```

مشاهدهٔ جزئیات یک Revision خاص:

```bash
kubectl rollout history deployment/web-app --revision=2
```

### بازگردانی به نسخهٔ قبلی (Rollback)

بازگشت به آخرین نسخهٔ سالم:

```bash
kubectl rollout undo deployment/web-app
```

بازگشت به یک Revision معین:

```bash
kubectl rollout undo deployment/web-app --to-revision=1
```

### توقف و ادامهٔ فرآیند Rollout

متوقف کردن موقت Rollout:

```bash
kubectl rollout pause deployment/web-app
```

ازسرگیری Rollout:

```bash
kubectl rollout resume deployment/web-app
```

### ریستارت بدون قطعی تمام پادهای یک Deployment (Rolling Restart)

```bash
kubectl rollout restart deployment/web-app
kubectl rollout restart daemonset/flannel-ds -n kube-flannel
```

---

## 10) StatefulSet و DaemonSet

### کار با StatefulSet

مشاهدهٔ StatefulSetها:

```bash
kubectl get statefulset
# یا اختصار:
kubectl get sts
```

تغییر مقیاس یک StatefulSet:

```bash
kubectl scale statefulset my-database --replicas=3
```

### کار با DaemonSet

مشاهدهٔ DaemonSetها در تمام Namespaceها:

```bash
kubectl get daemonsets -A
# یا اختصار:
kubectl get ds -A
```

مشاهدهٔ جزئیات یک DaemonSet:

```bash
kubectl describe ds kube-flannel-ds -n kube-flannel
```

---

## 11) شبکه، سرویس‌ها (Services) و Ingress

### ساخت سرویس با دستور `expose`

ساخت سرویس پیش‌فرض داخلی (ClusterIP):

```bash
kubectl expose deployment web-app --port=80 --target-port=80 --name=web-service
```

ساخت سرویس قابل دسترس روی IP نودها (NodePort):

```bash
kubectl expose deployment web-app --type=NodePort --port=80 --target-port=80 --name=web-nodeport
```

ساخت سرویس با LoadBalancer خارجی:

```bash
kubectl expose deployment web-app --type=LoadBalancer --port=80 --target-port=80 --name=web-lb
```

### مشاهده و بررسی سرویس‌ها و اندپوینت‌ها

مشاهدهٔ سرویس‌ها:

```bash
kubectl get services
# یا اختصار:
kubectl get svc -o wide
```

مشاهدهٔ Endpointهای نگاشت‌شده به سرویس (IP پادهای پشت سرویس):

```bash
kubectl get endpoints <service-name>
# یا اختصار:
kubectl get ep <service-name>
```

### مدیریت Ingress

مشاهدهٔ Ingressها:

```bash
kubectl get ingress
# یا اختصار:
kubectl get ing
```

مشاهدهٔ تنظیمات و Ruleهای یک Ingress:

```bash
kubectl describe ingress <ingress-name>
```

---

## 12) مدیریت ConfigMaps و Secrets

### کار با ConfigMap

ساخت ConfigMap از روی مقادیر متنی مستقیم (Literal):

```bash
kubectl create configmap app-config --from-literal=APP_ENV=production --from-literal=LOG_LEVEL=info
```

ساخت ConfigMap از روی یک فایل کامل:

```bash
kubectl create configmap nginx-config --from-file=nginx.conf
```

ساخت ConfigMap از روی یک پوشه شامل چندین فایل:

```bash
kubectl create configmap config-dir --from-file=./configs/
```

مشاهده و خواندن محتوای ConfigMap:

```bash
kubectl get configmaps
# یا اختصار:
kubectl get cm
kubectl get cm app-config -o yaml
```

### کار با Secret

ساخت Secret متنی (Generic / Opaque):

```bash
kubectl create secret generic db-credentials --from-literal=username=admin --from-literal=password='P@ssw0rd123'
```

ساخت Secret برای گواهی‌نامهٔ SSL/TLS:

```bash
kubectl create secret tls web-tls --cert=path/to/tls.crt --key=path/to/tls.key
```

ساخت Secret برای احراز هویت در رجیستری اختصاصی داکر (Docker Registry):

```bash
kubectl create secret docker-registry private-repo-secret \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=<username> \
  --docker-password=<password> \
  --docker-email=<email>
```

مشاهدهٔ Secretها:

```bash
kubectl get secrets
```

رمزگشایی و مشاهدهٔ مقدار خام یک Secret ذخیره‌شده در فرمت Base64:

```bash
kubectl get secret db-credentials -o jsonpath='{.data.password}' | base64 --decode
```

---

## 13) حافظه و ذخیره‌سازی پایدار (Volumes & Storage)

### بررسی StorageClassها

```bash
kubectl get storageclass
# یا اختصار:
kubectl get sc
```

### بررسی Persistent Volumes (PV)

```bash
kubectl get pv
kubectl describe pv <pv-name>
```

### بررسی Persistent Volume Claims (PVC)

```bash
kubectl get pvc
kubectl describe pvc <pvc-name>
```

حذف یک PVC:

```bash
kubectl delete pvc <pvc-name>
```

---

## 14) کنترل دسترسی، امنیت و کاربران (RBAC & Service Accounts)

### مدیریت Service Accountها

لیست اکانت‌های سرویس:

```bash
kubectl get serviceaccounts
# یا اختصار:
kubectl get sa
```

ساخت Service Account جدید:

```bash
kubectl create serviceaccount build-robot
```

تولید توکن موقت برای یک Service Account:

```bash
kubectl create token build-robot
```

### کار با Role و RoleBinding (محدود به یک Namespace)

تعریف یک Role برای دسترسی خواندن پادها:

```bash
kubectl create role pod-reader --verb=get,list,watch --resource=pods
```

اتصال دسترسی Role به یک Service Account:

```bash
kubectl create rolebinding read-pods-binding --role=pod-reader --serviceaccount=default:build-robot
```

### کار با ClusterRole و ClusterRoleBinding (درسطح کل کلاستر)

ساخت ClusterRole برای دسترسی به گره‌ها:

```bash
kubectl create clusterrole node-reader --verb=get,list,watch --resource=nodes
```

اتصال ClusterRole به کاربر:

```bash
kubectl create clusterrolebinding read-nodes-binding --clusterrole=node-reader --user=john
```

### بررسی و اعتبارسنجی دسترسی‌ها (Can-I)

آیا من دسترسی حذف پاد را دارم؟

```bash
kubectl auth can-i delete pods
```

بررسی دسترسی یک Service Account خاص:

```bash
kubectl auth can-i create deployments --as=system:serviceaccount:default:build-robot
```

بررسی دسترسی در یک Namespace معین:

```bash
kubectl auth can-i list secrets --namespace=production
```

---

## 15) مانیتورینگ منابع کلاستر و مصرف سخت‌افزار

> ⚠️ دستورات `kubectl top` نیازمند فعال بودن `Metrics-Server` در کلاستر هستند.

### بررسی میزان مصرف CPU و RAM در نودها

```bash
kubectl top nodes
```

### بررسی میزان مصرف پادها

```bash
kubectl top pods
```

بررسی مصرف پادها در همهٔ Namespaceها مرتب‌شده:

```bash
kubectl top pods -A
```

بررسی مصرف تفکیک‌شدهٔ هر کانتینر داخل پادها:

```bash
kubectl top pods --containers
```

---

## 16) مدیریت مانیفست‌ها و ساختار کدهای اعلامی (Declarative YAML)

### اعمال تغییرات از روی فایل مانیفست (Apply)

اعمال یک فایل:

```bash
kubectl apply -f deployment.yaml
```

اعمال تمام مانیفست‌های داخل یک پوشه:

```bash
kubectl apply -f ./manifests/
```

اعمال بازگشتی تمام زیرپوشه‌ها:

```bash
kubectl apply -R -f ./manifests/
```

اعمال مستقیم از طریق URL اینترنتی:

```bash
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

### مقایسهٔ تغییرات مانیفست محلی با وضعیت زنده در کلاستر (Diff)

```bash
kubectl diff -f deployment.yaml
```

### ویرایش مستقیم آبجکت‌های زنده در کلاستر (Live Edit)

```bash
kubectl edit deployment/web-app
kubectl edit svc/web-service
```

### حذف منابع از روی فایل مانیفست

```bash
kubectl delete -f deployment.yaml
```

---

## 17) فیلتر کردن خروجی‌ها و استخراج پیشرفته (JSONPath & Formatting)

### خروجی در قالب YAML یا JSON

```bash
kubectl get pod <pod-name> -o yaml
kubectl get svc <service-name> -o json
```

### استخراج فیلدهای دلخواه با استفاده از JSONPath

استخراج تمام IPهای پادها در کلاستر:

```bash
kubectl get pods -o jsonpath='{.items[*].status.podIP}'
```

استخراج نام نود و وضعیت Ready بودن آن:

```bash
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.conditions[?(@.type=="Ready")].status}{"\n"}{end}'
```

استخراج نام کانتینرهای موجود در یک پاد:

```bash
kubectl get pods <pod-name> -o jsonpath='{.spec.containers[*].name}'
```

### مرتب‌سازی خروجی‌ها (Sort-by)

مرتب‌سازی پادها بر اساس زمان ساخت:

```bash
kubectl get pods --sort-by='.metadata.creationTimestamp'
```

مرتب‌سازی پادها بر اساس تعداد ریستارت:

```bash
kubectl get pods --sort-by='.status.containerStatuses[0].restartCount'
```

---

## 18) رویدادها و عیب‌یابی عمیق (Events)

### مشاهدهٔ رویدادهای Namespace فعلی

```bash
kubectl get events
```

مشاهدهٔ تمام رویدادهای کلاستر مرتب‌شده بر اساس زمان رخ‌داد (بسیار کاربردی در دیباگ):

```bash
kubectl get events -A --sort-by='.metadata.creationTimestamp'
```

فیلتر رویدادها فقط برای موارد اخطار و ارور (Warning):

```bash
kubectl get events --field-selector type=Warning
```

مشاهدهٔ رویدادهای مختص یک پاد خاص:

```bash
kubectl get events --field-selector involvedObject.name=<pod-name>
```

---

## 19) دستورات کلیدی راه‌اندازی و مدیریت کلاستر با `kubeadm`

### راه‌اندازی کلاستر اولیه (Master / Control-plane)

```bash
kubeadm init --pod-network-cidr=10.244.0.0/16 --control-plane-endpoint="LOAD_BALANCER_DNS:6443" --upload-certs
```

### دریافت دستور الحاق (Join) برای اضافه کردن Worker Node

تولید دستور اتصال همراه توکن جدید:

```bash
kubeadm token create --print-join-command
```

اتصال نود کارگر (اجرا روی ماشین Worker):

```bash
kubeadm join <control-plane-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

### اضافه کردن Control-plane دوم یا جدید به کلاستر (HA Setup)

آپلود مجدد گواهی‌ها روی Master اول و دریافت کلید:

```bash
kubeadm init phase upload-certs --upload-certs
```

دستور اتصال Control-plane دوم (اجرا روی ماشین Master دوم):

```bash
kubeadm join <endpoint>:6443 --token <token> \
  --discovery-token-ca-cert-hash sha256:<hash> \
  --control-plane --certificate-key <cert-key>
```

### مدیریت توکن‌ها و سرتیفیکیت‌ها

مشاهدهٔ لیست توکن‌های فعال:

```bash
kubeadm token list
```

بررسی تاریخ انقضای تمام گواهینامه‌های کلاستر:

```bash
kubeadm certs check-expiration
```

تمدید دستی تمام گواهینامه‌ها پیش از انقضا:

```bash
kubeadm certs renew all
```

### ریست کامل نود و بازگردانی به تنظیمات پیش‌فرض

```bash
kubeadm reset -f
```

---

## 20) جدول اختصارات پرکاربرد کوبرنتیز (Shortnames)

برای تایپ سریع‌تر می‌توانید به‌جای نام‌های طولانی از اختصارات زیر استفاده کنید:

| نام منبع (Resource) | نام اختصاری (Shortname) | نام منبع (Resource) | نام اختصاری (Shortname) |
| :--- | :--- | :--- | :--- |
| **Pods** | `po` | **PersistentVolumeClaims** | `pvc` |
| **Deployments** | `deploy` | **PersistentVolumes** | `pv` |
| **Services** | `svc` | **StorageClasses** | `sc` |
| **Namespaces** | `ns` | **ServiceAccounts** | `sa` |
| **ConfigMaps** | `cm` | **Ingress** | `ing` |
| **DaemonSets** | `ds` | **Endpoints** | `ep` |
| **StatefulSets** | `sts` | **Nodes** | `no` |
| **HorizontalPodAutoscaler**| `hpa` | **NetworkPolicies** | `netpol` |
| **ResourceQuotas** | `quota` | **ClusterRoles** | `cr` |

---

## 21) جریان کاری روزمرهٔ پیشنهادی (Daily Workflow)

### 1. بررسی وضعیت عمومی کلاستر و نودها

```bash
kubectl get nodes -o wide
kubectl top nodes
```

### 2. دیپلوی اپلیکیشن و تست مقدماتی

```bash
# ایجاد دیپلویمنت
kubectl apply -f deployment.yaml

# مشاهده وضعیت استقرار
kubectl rollout status deployment/my-app

# بررسی وضعیت پادها
kubectl get pods -l app=my-app -o wide
```

### 3. در صورت بروز خطا و مشکل (Debugging Flow)

```bash
# گام اول: مشاهده وضعیت و علت کرش پاد
kubectl describe pod <failing-pod-name>

# گام دوم: خواندن لاگ کانتینر
kubectl logs <failing-pod-name> --previous

# گام سوم: بررسی رویدادهای زنده کلاستر
kubectl get events --sort-by='.metadata.creationTimestamp'

# گام چهارم: ورود به محیط پاد جهت بررسی شبکه و کانفیگ
kubectl exec -it <pod-name> -- sh
```

### 4. اعمال آپدیت و چرخهٔ Rollout

```bash
# آپدیت تصویر
kubectl set image deployment/my-app my-app=my-app:v2

# در صورت اشکال: بازگردانی فوری به نسخه پایدار قبل
kubectl rollout undo deployment/my-app
```

---

## 22) نکات طلایی و خطاهای رایج (Troubleshooting & Best Practices)

- **خطای `CrashLoopBackOff`:**  
  کانتینر بلافاصله پس از اجرا متوقف می‌شود. با `kubectl logs <pod-name> --previous` و `kubectl describe pod <pod-name>` علت اصلی خطای کد یا تنظیمات را بررسی کنید.

- **خطای `ImagePullBackOff` / `ErrImagePull`:**  
  نام Image یا تگ آن اشتباه است، یا دسترسی به Docker Registry به Secret (نوع `docker-registry`) نیاز دارد.

- **خطای `Pending` در وضعیت پاد:**  
  منابع کافی (CPU/RAM) روی هیچ نودی وجود ندارد، نودها در حالت `Cordon` هستند، یا Taint تعریف‌شده با Toleration پاد سازگار نیست (`kubectl describe pod` علت را نشان می‌دهد).

- **خطای `OOMKilled` (Exit Code 137):**  
  پاد بیش از حد مجاز تعیین‌شده در `limits.memory` رم مصرف کرده و توسط هسته سیستم‌عامل متوقف شده است. باید میزان Memory Limit افزایش یابد.

- **همیشه پیش از حذف یا تغییرات بزرگ از `--dry-run=client` استفاده کنید:**

  ```bash
  kubectl apply -f manifest.yaml --dry-run=client
  ```

- **ترافیک سرویس‌ها را قبل از تنظیم Ingress با `port-forward` بررسی کنید:**

  ```bash
  kubectl port-forward svc/my-service 8080:80
  ```
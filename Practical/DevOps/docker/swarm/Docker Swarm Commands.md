
---

# جزوه کامل Docker Swarm Commands

## فهرست

1. مفاهیم اصلی
2. دستورات راه‌اندازی Swarm
3. دستورات مدیریت Node
4. دستورات Service
5. دستورات Network در Swarm
6. دستورات Stack
7. دستورات Secret
8. دستورات Config
9. دستورات Inspect / Logs / Debug
10. دستورات نگهداری و عیب‌یابی
11. سناریوهای پرکاربرد
12. Cheat Sheet نهایی

---

# 1) مفاهیم اصلی

در Docker Swarm چند مفهوم مهم داریم:

- **Manager**: نود مدیریتی
- **Worker**: نود اجرایی
- **Service**: تعریف یک سرویس در Swarm
- **Task**: هر instance از یک service
- **Overlay Network**: شبکه بین نودها
- **Stack**: مجموعه‌ای از سرویس‌ها با فایل Compose
- **Secret**: اطلاعات حساس مثل پسورد
- **Config**: فایل کانفیگ غیرمحرمانه

---

# 2) دستورات راه‌اندازی Swarm

## ایجاد Swarm

```bash
docker swarm init
```

## ایجاد Swarm با IP مشخص

```bash
docker swarm init --advertise-addr 192.168.64.11
```

## اگر چند اینترفیس داری، آدرس listen هم مشخص کن

```bash
docker swarm init --advertise-addr 192.168.64.11 --listen-addr 192.168.64.11:2377
```

## مشاهده وضعیت join token برای worker

```bash
docker swarm join-token worker
```

## مشاهده وضعیت join token برای manager

```bash
docker swarm join-token manager
```

## چرخاندن token مربوط به worker

```bash
docker swarm join-token --rotate worker
```

## چرخاندن token مربوط به manager

```bash
docker swarm join-token --rotate manager
```

## Join کردن یک Worker به Swarm

```bash
docker swarm join --token <WORKER_TOKEN> 192.168.64.11:2377
```

## Join کردن یک Manager به Swarm

```bash
docker swarm join --token <MANAGER_TOKEN> 192.168.64.11:2377
```

## خروج از Swarm روی Worker

```bash
docker swarm leave
```

## خروج اجباری از Swarm روی Manager

```bash
docker swarm leave --force
```

## نمایش وضعیت کلی Swarm

```bash
docker info
```

یا:

```bash
docker system info
```

---

# 3) دستورات مدیریت Node

## لیست همه نودها

```bash
docker node ls
```

## مشاهده جزئیات یک نود

```bash
docker node inspect <NODE>
```

## مشاهده خلاصه و خواناتر

```bash
docker node inspect <NODE> --pretty
```

## Promote کردن Worker به Manager

```bash
docker node promote <NODE>
```

مثال:

```bash
docker node promote worker-1
```

## Demote کردن Manager به Worker

```bash
docker node demote <NODE>
```

## تغییر Availability به Active

```bash
docker node update --availability active <NODE>
```

## تغییر Availability به Pause

```bash
docker node update --availability pause <NODE>
```

## تغییر Availability به Drain

```bash
docker node update --availability drain <NODE>
```

## اضافه کردن Label به Node

```bash
docker node update --label-add db=true <NODE>
```

## حذف Label از Node

```bash
docker node update --label-rm db <NODE>
```

## حذف نود از Swarm

```bash
docker node rm <NODE>
```

## حذف اجباری نود

```bash
docker node rm --force <NODE>
```

---

# 4) دستورات Service

## ساخت سرویس ساده

```bash
docker service create --name nginx-test nginx:alpine
```

## ساخت سرویس با تعداد Replica

```bash
docker service create --name nginx-test --replicas 3 nginx:alpine
```

## ساخت سرویس با publish کردن پورت

```bash
docker service create \
  --name nginx-test \
  --replicas 3 \
  --publish published=8080,target=80 \
  nginx:alpine
```

## ساخت سرویس روی یک Overlay Network

```bash
docker service create \
  --name nginx-test \
  --network swarm-test-net \
  --replicas 3 \
  nginx:alpine
```

## لیست سرویس‌ها

```bash
docker service ls
```

## مشاهده جزئیات یک سرویس

```bash
docker service inspect <SERVICE>
```

## مشاهده زیباتر

```bash
docker service inspect <SERVICE> --pretty
```

## مشاهده Taskهای یک سرویس

```bash
docker service ps <SERVICE>
```

## مشاهده Taskها با جزئیات کامل

```bash
docker service ps <SERVICE> --no-trunc
```

## دیدن لاگ سرویس

```bash
docker service logs <SERVICE>
```

## لاگ به‌صورت لحظه‌ای

```bash
docker service logs -f <SERVICE>
```

## محدود کردن تعداد لاگ

```bash
docker service logs --tail 100 <SERVICE>
```

## تغییر تعداد Replica

```bash
docker service scale nginx-test=5
```

## آپدیت image سرویس

```bash
docker service update --image nginx:latest nginx-test
```

## Force update برای restart مجدد taskها

```bash
docker service update --force nginx-test
```

## تغییر تعداد Replica با update

```bash
docker service update --replicas 2 nginx-test
```

## اضافه کردن Network به سرویس

```bash
docker service update --network-add swarm-test-net nginx-test
```

## حذف Network از سرویس

```bash
docker service update --network-rm swarm-test-net nginx-test
```

## اضافه کردن Env

```bash
docker service update --env-add APP_ENV=prod nginx-test
```

## حذف Env

```bash
docker service update --env-rm APP_ENV nginx-test
```

## اضافه کردن Port

```bash
docker service update --publish-add published=9090,target=80 nginx-test
```

## حذف Port

```bash
docker service update --publish-rm 9090 nginx-test
```

## محدود کردن سرویس به نودهای خاص با constraint

```bash
docker service create \
  --name db \
  --constraint 'node.labels.db == true' \
  postgres:16
```

## ساخت سرویس global

روی هر نود یک Task:

```bash
docker service create \
  --name agent \
  --mode global \
  alpine sleep 100000
```

## حذف سرویس

```bash
docker service rm nginx-test
```

---

# 5) دستورات Network در Swarm

## لیست Networkها

```bash
docker network ls
```

## ساخت Overlay Network

```bash
docker network create --driver overlay swarm-test-net
```

## ساخت Overlay Network attachable

```bash
docker network create --driver overlay --attachable swarm-test-net
```

## ساخت encrypted overlay network

```bash
docker network create \
  --driver overlay \
  --opt encrypted \
  secure-net
```

## مشاهده جزئیات Network

```bash
docker network inspect swarm-test-net
```

## حذف Network

```bash
docker network rm swarm-test-net
```

---

# 6) دستورات Stack


<p dir="rtl"> Stack برای Deploy اپلیکیشن با Compose در Swarm استفاده می‌شود. </p>


## Deploy یک Stack

```bash
docker stack deploy -c stack.yml demo
```

## لیست Stackها

```bash
docker stack ls
```

## لیست سرویس‌های یک Stack

```bash
docker stack services demo
```

## لیست Taskهای یک Stack

```bash
docker stack ps demo
```

## حذف Stack

```bash
docker stack rm demo
```

## مشاهده Compose config قبل از deploy

```bash
docker compose config
```

> در Swarm معمولاً deploy با `docker stack deploy` انجام می‌شود، نه `docker compose up`.

---

# 7) دستورات Secret

## ساخت Secret از stdin

```bash
printf 'MyStrongPassword' | docker secret create db_password -
```

## ساخت Secret از فایل

```bash
docker secret create app_key ./app_key.txt
```

## لیست Secretها

```bash
docker secret ls
```

## مشاهده جزئیات Secret

```bash
docker secret inspect db_password
```

## حذف Secret

```bash
docker secret rm db_password
```

## استفاده از Secret در Service

```bash
docker service create \
  --name myapp \
  --secret db_password \
  nginx:alpine
```

## افزودن Secret به سرویس موجود

```bash
docker service update --secret-add db_password myapp
```

## حذف Secret از سرویس

```bash
docker service update --secret-rm db_password myapp
```

---

# 8) دستورات Config

Config برای فایل‌های معمولی و غیرحساس.

## ساخت Config از فایل

```bash
docker config create nginx_conf ./nginx.conf
```

## لیست Configها

```bash
docker config ls
```

## مشاهده Config

```bash
docker config inspect nginx_conf
```

## حذف Config

```bash
docker config rm nginx_conf
```

## استفاده از Config در Service

```bash
docker service create \
  --name web \
  --config source=nginx_conf,target=/etc/nginx/nginx.conf \
  nginx:alpine
```

## افزودن Config به سرویس

```bash
docker service update \
  --config-add source=nginx_conf,target=/etc/nginx/nginx.conf \
  web
```

## حذف Config از سرویس

```bash
docker service update \
  --config-rm nginx_conf \
  web
```

---

# 9) دستورات Inspect / Logs / Debug

## بررسی وضعیت Swarm

```bash
docker info
```

## بررسی سرویس

```bash
docker service inspect nginx-test --pretty
```

## بررسی نود

```bash
docker node inspect worker-2 --pretty
```

## بررسی Network

```bash
docker network inspect swarm-test-net
```

## بررسی Taskها

```bash
docker service ps nginx-test
```

## بررسی containerهای محلی روی یک نود

```bash
docker ps
```

## دیدن containerهای یک سرویس روی همان نود

```bash
docker ps --filter label=com.docker.swarm.service.name=nginx-test
```

## دیدن لاگ container محلی

```bash
docker logs <CONTAINER_ID>
```

## مانیتور زنده

```bash
watch -n 2 'docker service ps nginx-test'
```

---

# 10) دستورات نگهداری و عیب‌یابی

## Drain کردن یک نود

```bash
docker node update --availability drain worker-2
```

## بازگرداندن به Active

```bash
docker node update --availability active worker-2
```

## Rebalance دستی با force update

```bash
docker service update --force nginx-test
```

## بررسی اینکه image قابل دانلود است

```bash
docker pull nginx:alpine
```

## بررسی پورت‌های Swarm

روی لینوکس:

```bash
ss -tulpn | grep -E '2377|7946|4789'
```

## تست اتصال داخلی

```bash
ping -c 2 192.168.64.12
```

## تست پاسخ سرویس منتشرشده

```bash
curl -I http://192.168.64.11:8080
```

## حذف کامل سرویس تست

```bash
docker service rm nginx-test
```

## حذف Network تست

```bash
docker network rm swarm-test-net
```

---

# 11) سناریوهای پرکاربرد

---

## سناریو 1: ساخت Swarm از صفر

### روی Manager اول

```bash
docker swarm init --advertise-addr 192.168.64.11
```

### گرفتن tokenها

```bash
docker swarm join-token manager
docker swarm join-token worker
```

### روی Manager دوم

```bash
docker swarm join --token <MANAGER_TOKEN> 192.168.64.11:2377
```

### روی Worker

```bash
docker swarm join --token <WORKER_TOKEN> 192.168.64.11:2377
```

---

## سناریو 2: Promote کردن Worker به Manager

```bash
docker node promote worker-1
docker node ls
```

---

## سناریو 3: اجرای Nginx در Swarm

```bash
docker network create --driver overlay --attachable swarm-test-net

docker service create \
  --name nginx-test \
  --network swarm-test-net \
  --replicas 3 \
  --publish published=8080,target=80 \
  nginx:alpine
```

بررسی:

```bash
docker service ls
docker service ps nginx-test
```

---

## سناریو 4: مقیاس‌پذیری

```bash
docker service scale nginx-test=5
```

یا:

```bash
docker service update --replicas 5 nginx-test
```

---

## سناریو 5: آپدیت rolling

```bash
docker service update \
  --image nginx:latest \
  --update-parallelism 1 \
  --update-delay 10s \
  nginx-test
```

---

## سناریو 6: Rollback

اگر update خراب شد:

```bash
docker service rollback nginx-test
```

---

## سناریو 7: اجرای سرویس فقط روی نودهای labelدار

### Label دادن

```bash
docker node update --label-add database=true worker-2
```

### Deploy با constraint

```bash
docker service create \
  --name postgres \
  --constraint 'node.labels.database == true' \
  postgres:16
```

---

## سناریو 8: اجرای Agent روی همه نودها

```bash
docker service create \
  --name node-exporter \
  --mode global \
  prom/node-exporter
```

---

# 12) Cheat Sheet نهایی

## Swarm

```bash
docker swarm init --advertise-addr <IP>
docker swarm join-token manager
docker swarm join-token worker
docker swarm join --token <TOKEN> <MANAGER_IP>:2377
docker swarm leave
docker swarm leave --force
docker swarm join-token --rotate manager
docker swarm join-token --rotate worker
```

## Node

```bash
docker node ls
docker node inspect <NODE> --pretty
docker node promote <NODE>
docker node demote <NODE>
docker node update --availability drain <NODE>
docker node update --availability active <NODE>
docker node update --label-add key=value <NODE>
docker node rm <NODE>
```

## Service

```bash
docker service ls
docker service create --name <NAME> --replicas 3 <IMAGE>
docker service ps <SERVICE>
docker service inspect <SERVICE> --pretty
docker service logs -f <SERVICE>
docker service scale <SERVICE>=5
docker service update --image <IMAGE> <SERVICE>
docker service update --force <SERVICE>
docker service rollback <SERVICE>
docker service rm <SERVICE>
```

## Network

```bash
docker network ls
docker network create --driver overlay --attachable <NET>
docker network inspect <NET>
docker network rm <NET>
```

## Stack

```bash
docker stack deploy -c stack.yml <STACK>
docker stack ls
docker stack services <STACK>
docker stack ps <STACK>
docker stack rm <STACK>
```

## Secret / Config

```bash
docker secret create <NAME> <FILE|->
docker secret ls
docker secret rm <NAME>

docker config create <NAME> <FILE>
docker config ls
docker config rm <NAME>
```

---

# نکته مهم پایانی

اگر منظورت از «تمام کامندهای Docker Swarm» این باشد که **همه subcommandهای مرتبط با Swarm** را بخواهی، این‌ها مهم‌ترین و عملی‌ترین‌ها بودند:

- `docker swarm`
- `docker node`
- `docker service`
- `docker stack`
- `docker secret`
- `docker config`
- `docker network` در زمینه Swarm

اگر بخواهی، در مرحله بعدی من می‌توانم همین جزوه را به یکی از این ۳ شکل برایت آماده کنم:

1. **نسخه خلاصه ۱ صفحه‌ای Cheat Sheet**
2. **نسخه حرفه‌ای با مثال واقعی برای هر دستور**
3. **نسخه PDF/جزوه مرتب‌شده برای ذخیره و مطالعه**

اگر خواستی، همین الآن نسخه **PDF جزوه Docker Swarm** را هم برایت آماده می‌کنم.
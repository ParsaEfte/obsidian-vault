
---

## 1) نصب و بررسی نسخه

```bash
git --version
```

---

## 2) تنظیمات اولیه (Identity & Config)

### تنظیم نام و ایمیل به‌صورت سراسری

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### تنظیم نام و ایمیل فقط برای یک پروژه

```bash
git config user.name "Your Name"
git config user.email "you@example.com"
```

### مشاهدهٔ تنظیمات

```bash
git config --list
git config --global --list
git config user.name
git config user.email
```

### ویرایش فایل تنظیمات

```bash
git config --global --edit
git config --edit
```

### تعیین شاخهٔ پیش‌فرض برای پروژه‌های جدید

```bash
git config --global init.defaultBranch main
```

### فعال‌کردن رنگ خروجی‌ها

```bash
git config --global color.ui auto
```

### ساخت Aliasهای کاربردی

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm commit
git config --global alias.lg "log --oneline --graph --decorate --all"
```

استفاده:

```bash
git st
git lg
```

---

# 3) ساخت یا دریافت Repository

## ساخت پروژهٔ Git جدید

```bash
git init
```

ساخت با نام شاخهٔ اولیهٔ `main`:

```bash
git init -b main
```

ساخت پروژه در یک پوشهٔ مشخص:

```bash
git init my-project
```

---

## دریافت پروژه از ریموت (Clone)

```bash
git clone <repository-url>
```

مثال HTTPS:

```bash
git clone https://github.com/user/project.git
```

مثال SSH:

```bash
git clone git@github.com:user/project.git
```

کلون در یک پوشه با نام دلخواه:

```bash
git clone https://github.com/user/project.git my-local-project
```

کلون فقط آخرین تاریخچه (Shallow Clone):

```bash
git clone --depth 1 https://github.com/user/project.git
```

کلون یک شاخهٔ مشخص:

```bash
git clone --branch develop https://github.com/user/project.git
```

---

# 4) بررسی وضعیت و تاریخچه

## وضعیت فایل‌ها

```bash
git status
```

خروجی کوتاه:

```bash
git status -s
```

---

## مشاهدهٔ تغییرات

تغییرات فایل‌هایی که هنوز Stage نشده‌اند:

```bash
git diff
```

تغییرات یک فایل مشخص:

```bash
git diff filename.txt
```

تغییرات Stage‌شده:

```bash
git diff --staged
```

یا:

```bash
git diff --cached
```

مقایسه با آخرین Commit:

```bash
git diff HEAD
```

---

## مشاهدهٔ تاریخچهٔ Commitها

```bash
git log
```

نمایش خلاصه:

```bash
git log --oneline
```

نمایش گراف شاخه‌ها:

```bash
git log --oneline --graph --decorate --all
```

نمایش آخرین ۵ Commit:

```bash
git log -5
```

نمایش Commitهای یک فایل:

```bash
git log -- filename.txt
```

نمایش تغییرات هر Commit:

```bash
git log -p
```

جست‌وجو در پیام Commitها:

```bash
git log --grep="fix"
```

جست‌وجو بر اساس نویسنده:

```bash
git log --author="Ali"
```

نمایش یک Commit خاص:

```bash
git show <commit-hash>
```

مثال:

```bash
git show a1b2c3d
```

---

# 5) چرخهٔ اصلی Git: Add، Commit و Restore

## افزودن فایل به Stage

یک فایل:

```bash
git add filename.txt
```

چند فایل:

```bash
git add file1.txt file2.txt
```

تمام تغییرات پوشهٔ فعلی:

```bash
git add .
```

تمام تغییرات پروژه، از جمله حذف فایل‌ها:

```bash
git add -A
```

افزودن تعاملی بخش‌هایی از فایل:

```bash
git add -p
```

---

## ثبت تغییرات (Commit)

```bash
git commit -m "Your commit message"
```

مثال:

```bash
git commit -m "Add user authentication"
```

افزودن و Commit فایل‌های **قبلاً Track‌شده** در یک دستور:

```bash
git commit -am "Update configuration"
```

ویرایش آخرین Commit:

```bash
git commit --amend
```

تغییر فقط پیام آخرین Commit:

```bash
git commit --amend -m "New commit message"
```

اضافه‌کردن تغییرات جاافتاده به آخرین Commit بدون تغییر پیام:

```bash
git add .
git commit --amend --no-edit
```

> ⚠️ بعد از Push کردن Commit روی Repository مشترک، استفاده از `--amend` نیازمند دقت است.

---

## خارج‌کردن فایل از Stage

خارج‌کردن یک فایل از Stage بدون حذف تغییرات:

```bash
git restore --staged filename.txt
```

روش قدیمی‌تر:

```bash
git reset HEAD filename.txt
```

خارج‌کردن همهٔ فایل‌ها از Stage:

```bash
git restore --staged .
```

---

## لغو تغییرات فایل‌ها

بازگرداندن یک فایل به آخرین وضعیت Commit‌شده:

```bash
git restore filename.txt
```

بازگرداندن همهٔ تغییرات محلیِ فایل‌های Track‌شده:

```bash
git restore .
```

> ⚠️ این دستور تغییرات محلی را حذف می‌کند و معمولاً قابل بازگشت نیست.

---

# 6) مدیریت Branchها

## مشاهدهٔ شاخه‌ها

شاخه‌های محلی:

```bash
git branch
```

شاخه‌های ریموت:

```bash
git branch -r
```

همهٔ شاخه‌ها:

```bash
git branch -a
```

نمایش آخرین Commit هر شاخه:

```bash
git branch -v
```

---

## ساخت شاخه

```bash
git branch <branch-name>
```

مثال:

```bash
git branch feature/login
```

ساخت شاخه از یک Commit مشخص:

```bash
git branch feature/login <commit-hash>
```

---

## جابه‌جایی بین شاخه‌ها

روش جدید و پیشنهادی:

```bash
git switch <branch-name>
```

مثال:

```bash
git switch develop
```

روش قدیمی:

```bash
git checkout <branch-name>
```

---

## ساخت و ورود هم‌زمان به یک شاخه

روش جدید:

```bash
git switch -c <branch-name>
```

مثال:

```bash
git switch -c feature/payment
```

روش قدیمی:

```bash
git checkout -b <branch-name>
```

---

## تغییر نام شاخه

تغییر نام شاخهٔ فعلی:

```bash
git branch -m <new-name>
```

مثال:

```bash
git branch -m main
```

تغییر نام یک شاخهٔ مشخص:

```bash
git branch -m <old-name> <new-name>
```

---

## حذف شاخه

حذف ایمن یک شاخهٔ Merge‌شده:

```bash
git branch -d <branch-name>
```

حذف اجباری شاخه:

```bash
git branch -D <branch-name>
```

مثال:

```bash
git branch -d feature/login
```

---

# 7) Merge کردن شاخه‌ها

ابتدا وارد شاخهٔ مقصد شوید:

```bash
git switch main
```

سپس شاخهٔ موردنظر را Merge کنید:

```bash
git merge feature/login
```

Merge بدون Fast-forward برای ایجاد Commit مشخص:

```bash
git merge --no-ff feature/login
```

لغو Merge در صورت Conflict یا مشکل:

```bash
git merge --abort
```

نمایش شاخه‌هایی که با شاخهٔ فعلی Merge شده‌اند:

```bash
git branch --merged
```

نمایش شاخه‌هایی که Merge نشده‌اند:

```bash
git branch --no-merged
```

---

# 8) Rebase

<p dir="rtl">Rebase یعنی انتقال Commitهای یک شاخه روی آخرین Commit شاخهٔ دیگر.</p>

```bash
git switch feature/login
git rebase main
```

<p dir="rtl">Rebase تعاملی برای ویرایش، حذف یا ادغام Commitها: </p>

```bash
git rebase -i HEAD~3
```

ادامهٔ Rebase بعد از حل Conflict:

```bash
git add .
git rebase --continue
```

لغو Rebase:

```bash
git rebase --abort
```

ردکردن Commit فعلی در Rebase:

```bash
git rebase --skip
```

> ⚠️ روی شاخه‌هایی که دیگران از آن استفاده می‌کنند، Rebase می‌تواند تاریخچه را تغییر دهد. با احتیاط استفاده شود.

---

# 9) اتصال به Remote Repository

## مشاهدهٔ ریموت‌ها

```bash
git remote
```

نمایش آدرس ریموت‌ها:

```bash
git remote -v
```

---

## افزودن Remote

```bash
git remote add origin <repository-url>
```

مثال:

```bash
git remote add origin git@github.com:username/project.git
```

---

## تغییر آدرس Remote

```bash
git remote set-url origin <new-url>
```

---

## حذف Remote

```bash
git remote remove origin
```

یا:

```bash
git remote rm origin
```

---

## اطلاعات یک Remote

```bash
git remote show origin
```

---

# 10) Push، Pull و Fetch

## اولین Push شاخه

```bash
git push -u origin main
```

بعد از این دستور، برای دفعات بعد کافی است بنویسید:

```bash
git push
```

---

## Push کردن شاخهٔ فعلی

```bash
git push
```

Push یک شاخهٔ مشخص:

```bash
git push origin feature/login
```

Push همهٔ شاخه‌ها:

```bash
git push --all origin
```

Push تگ‌ها:

```bash
git push --tags
```

---

## حذف شاخه از Remote

```bash
git push origin --delete <branch-name>
```

مثال:

```bash
git push origin --delete feature/login
```

---

## دریافت تغییرات بدون Merge (Fetch)

```bash
git fetch
```

دریافت از یک Remote مشخص:

```bash
git fetch origin
```

دریافت و حذف اطلاعات شاخه‌های حذف‌شدهٔ ریموت:

```bash
git fetch --prune
```

---

## دریافت و ادغام تغییرات (Pull)

```bash
git pull
```

Pull از شاخهٔ مشخص:

```bash
git pull origin main
```

Pull با Rebase:

```bash
git pull --rebase
```

تنظیم Pull به‌صورت پیش‌فرض با Rebase:

```bash
git config --global pull.rebase true
```

---

## Push اجباری

```bash
git push --force
```

روش امن‌تر و توصیه‌شده:

```bash
git push --force-with-lease
```

> ⚠️ `--force` ممکن است Commitهای دیگران را از ریموت حذف کند. تا حد امکان از `--force-with-lease` استفاده کنید.

---

# 11) حل Merge Conflict

هنگام Conflict ابتدا وضعیت را بررسی کنید:

```bash
git status
```

فایل‌های دارای Conflict را باز کنید. بخش‌های متناقض معمولاً به این شکل‌اند:

```text
<<<<<<< HEAD
تغییرات شاخهٔ فعلی
=======
تغییرات شاخهٔ دیگر
>>>>>>> feature/login
```

پس از اصلاح فایل:

```bash
git add <conflicted-file>
git commit
```

در Rebase:

```bash
git add <conflicted-file>
git rebase --continue
```

لغو عملیات:

```bash
git merge --abort
```

یا:

```bash
git rebase --abort
```

---

# 12) Undo و Reset

## Reset نرم — فقط جابه‌جایی HEAD

Commit حذف می‌شود اما تغییرات در Stage باقی می‌مانند:

```bash
git reset --soft HEAD~1
```

---

## Reset معمولی / Mixed — خروج از Stage

Commit حذف می‌شود؛ تغییرات در فایل‌ها باقی می‌مانند ولی Stage نیستند:

```bash
git reset HEAD~1
```

یا:

```bash
git reset --mixed HEAD~1
```

---

## Reset سخت — حذف کامل تغییرات

```bash
git reset --hard HEAD~1
```

بازگرداندن کامل شاخهٔ محلی به وضعیت Remote:

```bash
git fetch origin
git reset --hard origin/main
```

> ⚠️ `--hard` تغییرات محلی و Commitهای حذف‌شده را پاک می‌کند؛ پیش از اجرا مطمئن شوید.

---

## بازگرداندن یک Commit با Commit جدید

برای پروژه‌های مشترک، `revert` معمولاً امن‌تر از `reset` است:

```bash
git revert <commit-hash>
```

مثال:

```bash
git revert a1b2c3d
```

Revert کردن بدون ساخت Commit فوری:

```bash
git revert --no-commit <commit-hash>
```

لغو Revert:

```bash
git revert --abort
```

---

## بازیابی Commitهای ظاهراً حذف‌شده

```bash
git reflog
```

سپس بازگشت به Commit موردنظر:

```bash
git reset --hard <commit-hash>
```

---

# 13) Stash؛ ذخیرهٔ موقت تغییرات

## ذخیرهٔ تغییرات فعلی

```bash
git stash
```

ذخیره همراه پیام:

```bash
git stash push -m "WIP: working on login"
```

ذخیره همراه فایل‌های Untracked:

```bash
git stash -u
```

ذخیره همراه فایل‌های Ignore‌شده:

```bash
git stash -a
```

---

## مشاهدهٔ Stashها

```bash
git stash list
```

نمایش خلاصهٔ یک Stash:

```bash
git stash show stash@{0}
```

نمایش کامل تغییرات Stash:

```bash
git stash show -p stash@{0}
```

---

## بازگرداندن Stash

بازگرداندن بدون حذف Stash:

```bash
git stash apply
```

بازگرداندن یک Stash مشخص:

```bash
git stash apply stash@{1}
```

بازگرداندن و حذف Stash:

```bash
git stash pop
```

حذف یک Stash:

```bash
git stash drop stash@{0}
```

حذف همهٔ Stashها:

```bash
git stash clear
```

ساخت شاخه از Stash:

```bash
git stash branch feature/from-stash stash@{0}
```

---

# 14) مدیریت فایل‌ها

## حذف فایل از Git و سیستم

```bash
git rm filename.txt
```

سپس Commit کنید:

```bash
git commit -m "Remove filename.txt"
```

---

## حذف فایل فقط از Git، اما نگه‌داشتن آن در سیستم

```bash
git rm --cached filename.txt
```

مثال برای حذف فایل تنظیمات حساس از Repository:

```bash
git rm --cached .env
```

---

## تغییر نام فایل با Git

```bash
git mv old-name.txt new-name.txt
```

---

# 15) فایل `.gitignore`

برای نادیده‌گرفتن فایل‌ها و پوشه‌هایی که نباید وارد Git شوند، فایل زیر را بسازید:

```bash
touch .gitignore
```

نمونهٔ محتوا:

```gitignore
# وابستگی‌های Node.js
node_modules/

# متغیرهای محیطی
.env
.env.local

# فایل‌های لاگ
*.log

# فایل‌های IDE
.vscode/
.idea/

# فایل‌های سیستم‌عامل
.DS_Store
Thumbs.db

# خروجی Build
dist/
build/
```

بررسی اینکه چرا یک فایل Ignore شده است:

```bash
git check-ignore -v filename.txt
```

> اگر فایلی قبلاً Track شده باشد، اضافه‌کردن آن به `.gitignore` کافی نیست. ابتدا باید از Index حذف شود:
>
> ```bash
> git rm --cached filename.txt
> git commit -m "Stop tracking filename"
> ```

---

# 16) Tag؛ نسخه‌گذاری پروژه

## مشاهدهٔ تگ‌ها

```bash
git tag
```

جست‌وجوی تگ:

```bash
git tag -l "v1.*"
```

---

## ساخت Lightweight Tag

```bash
git tag v1.0.0
```

---

## ساخت Annotated Tag (توصیه‌شده)

```bash
git tag -a v1.0.0 -m "Release version 1.0.0"
```

تگ‌کردن یک Commit مشخص:

```bash
git tag -a v1.0.0 <commit-hash> -m "Release version 1.0.0"
```

---

## Push کردن Tag

یک تگ:

```bash
git push origin v1.0.0
```

همهٔ تگ‌ها:

```bash
git push origin --tags
```

---

## حذف Tag

حذف محلی:

```bash
git tag -d v1.0.0
```

حذف از Remote:

```bash
git push origin --delete v1.0.0
```

---

# 17) بررسی و مقایسهٔ Commitها و Branchها

مقایسهٔ دو Branch:

```bash
git diff main..develop
```

مشاهدهٔ Commitهای موجود در `develop` اما غایب در `main`:

```bash
git log main..develop
```

مشاهدهٔ تفاوت دو Commit:

```bash
git diff <commit1> <commit2>
```

مشاهدهٔ تغییرات یک Commit:

```bash
git show <commit-hash>
```

نمایش اینکه چه کسی هر خط فایل را تغییر داده است:

```bash
git blame filename.txt
```

نمایش تاریخچهٔ تغییر نام فایل:

```bash
git log --follow -- filename.txt
```

---

# 18) Cherry-pick

انتقال یک Commit مشخص از یک شاخه به شاخهٔ فعلی:

```bash
git cherry-pick <commit-hash>
```

مثال:

```bash
git switch main
git cherry-pick a1b2c3d
```

ادامه بعد از حل Conflict:

```bash
git add .
git cherry-pick --continue
```

لغو عملیات:

```bash
git cherry-pick --abort
```

---

# 19) Git Worktree

ساخت یک پوشهٔ کاری جداگانه برای کار هم‌زمان روی شاخه‌ای دیگر:

```bash
git worktree add ../project-feature feature/login
```

ساخت شاخهٔ جدید همراه Worktree:

```bash
git worktree add -b feature/payment ../project-payment main
```

نمایش Worktreeها:

```bash
git worktree list
```

حذف Worktree:

```bash
git worktree remove ../project-feature
```

---

# 20) Submodule

اضافه‌کردن یک Repository به‌عنوان زیرپروژه:

```bash
git submodule add <repository-url> <path>
```

مثال:

```bash
git submodule add https://github.com/example/library.git libs/library
```

کلون پروژه همراه Submoduleها:

```bash
git clone --recurse-submodules <repository-url>
```

دریافت Submoduleها بعد از Clone معمولی:

```bash
git submodule update --init --recursive
```

به‌روزرسانی Submoduleها:

```bash
git submodule update --remote --merge
```

---

# 21) Git Bisect؛ پیدا کردن Commit خراب‌کننده

شروع فرآیند:

```bash
git bisect start
```

اعلام Commit فعلی به‌عنوان خراب:

```bash
git bisect bad
```

اعلام یک Commit قدیمیِ سالم:

```bash
git bisect good <commit-hash>
```

پس از بررسی هر مرحله:

```bash
git bisect good
```

یا:

```bash
git bisect bad
```

پایان فرآیند و بازگشت به شاخهٔ اولیه:

```bash
git bisect reset
```

---

# 22) پاک‌سازی فایل‌های اضافی

نمایش فایل‌هایی که پاک خواهند شد، بدون حذف واقعی:

```bash
git clean -n
```

حذف فایل‌های Untracked:

```bash
git clean -f
```

حذف فایل‌ها و پوشه‌های Untracked:

```bash
git clean -fd
```

حذف فایل‌های Ignore‌شده نیز:

```bash
git clean -fdx
```

> ⚠️ پیش از `git clean` همیشه ابتدا `git clean -n` را اجرا کنید.

---

# 23) دستورات کاربردی برای بررسی Repository

بررسی سلامت Repository:

```bash
git fsck
```

نمایش آمار Repository:

```bash
git count-objects -v
```

فشرده‌سازی و بهینه‌سازی Repository:

```bash
git gc
```

بهینه‌سازی شدید:

```bash
git gc --aggressive
```

نمایش فایل‌های بزرگ در تاریخچه:

```bash
git rev-list --objects --all | sort -k 2
```

---

# 24) Git Hooks

مسیر Hookها:

```bash
.git/hooks/
```

نمونهٔ Hookهای رایج:

```text
pre-commit
prepare-commit-msg
commit-msg
post-commit
pre-push
pre-receive
post-receive
```

برای فعال‌کردن یک Hook نمونه:

```bash
cp .git/hooks/pre-commit.sample .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

---

# 25) جریان کاری روزمرهٔ پیشنهادی

## شروع کار روی یک قابلیت جدید

```bash
git switch main
git pull --rebase origin main
git switch -c feature/login
```

## ثبت تغییرات

```bash
git status
git add .
git commit -m "Implement login feature"
```

## به‌روزرسانی شاخه با تغییرات `main`

```bash
git fetch origin
git rebase origin/main
```

یا:

```bash
git merge origin/main
```

## ارسال شاخه به Remote

```bash
git push -u origin feature/login
```

## پس از Merge شدن Pull Request

```bash
git switch main
git pull origin main
git branch -d feature/login
git push origin --delete feature/login
```

---

# 26) خلاصهٔ سریع و ضروری

```bash
# وضعیت پروژه
git status

# دریافت آخرین تغییرات
git pull --rebase

# ساخت شاخه و ورود به آن
git switch -c feature/my-feature

# افزودن تغییرات
git add .

# ثبت تغییرات
git commit -m "Message"

# ارسال تغییرات
git push

# مشاهده تاریخچه
git log --oneline --graph --decorate --all

# برگشت تغییرات یک فایل
git restore filename.txt

# خارج کردن فایل از Stage
git restore --staged filename.txt

# ذخیره موقت تغییرات
git stash

# بازگرداندن ذخیره موقت
git stash pop

# ادغام شاخه
git switch main
git merge feature/my-feature

# حذف شاخه
git branch -d feature/my-feature
```

---

# 27) نکات مهم و خطاهای رایج

- قبل از هر `pull` یا `push`، وضعیت را بررسی کنید:

  ```bash
  git status
  ```

- Commitهای کوچک، واضح و هدفمند بسازید.

- پیام Commit را به‌صورت فعل امری و روشن بنویسید:

  ```bash
  git commit -m "Add Docker Swarm deployment files"
  git commit -m "Fix worker node join command"
  ```

- فایل‌های حساس مانند `.env`، کلیدهای SSH و رمزها را هرگز Commit نکنید.

- قبل از استفاده از دستورات مخرب مانند زیر، حتماً وضعیت و تاریخچه را بررسی کنید:

  ```bash
  git reset --hard
  git clean -fd
  git push --force
  ```

- اگر فکر کردید Commit یا Branch را اشتباهی حذف کرده‌اید، ابتدا این دستور را بزنید:

  ```bash
  git reflog
  ```

---
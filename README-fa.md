<p align="center">
  <img src="https://raw.githubusercontent.com/peditx/luci-theme-peditx/refs/heads/main/luasrc/brand.png" alt="PeDitXOS" width="520">
</p>

<h1 align="center">ezPeDitXOS</h1>

یک **نصب‌کنندهٔ زندهٔ مبتنی بر Debian** که فریمور **PeDitXOS** / OpenWrt را از
طریق یک صفحهٔ وب روی هارد دیسک دستگاه‌های X86 و X86-64 می‌نویسد.

استیک را بوت کنید → `http://<ip>/` را در مرورگر باز کنید → فایل `.img` را
آپلود کنید یا لینک دانلودش را بدهید → SHA-256 را چک کنید → دیسک مقصد را
انتخاب کنید → نوشتن (`dd`) و بزرگ‌کردن پارتیشن.

[English](README.md)

## تصاویر

کل نصب‌کننده یک ویزارد شش‌مرحله‌ای است. زبان پیش‌فرض انگلیسی است و با کلید
`فارسی` بالای صفحه به فارسی برمی‌گردد؛ چیدمان RTL می‌شود و فونت Dana جایگزین
می‌شود.

| | |
|---|---|
| ![خوش‌آمد](docs/screenshots/01-welcome.png) | ![تنظیمات](docs/screenshots/02-settings.png) |
| **۱ · خوش‌آمد** — این چیست، چه کسی ساخته، فریمور از کجا می‌آید | **۲ · تنظیمات** — ساعت و کارت‌شبکه‌ها، تا تاریخ غلط قبل از نوشتن دیده شود |
| ![فایل](docs/screenshots/03-image.png) | ![بررسی hash](docs/screenshots/04-verify.png) |
| **۳ · فایل** — آپلود، دانلود از لینک، یا انتخاب فایل روی استیک (با صفحه‌بندی) | **۴ · بررسی hash** — محاسبهٔ SHA-256 و مقایسه با hash رسمی، یا رد شدن |
| ![دیسک](docs/screenshots/05-disk.png) | ![نصب](docs/screenshots/06-install.png) |
| **۵ · دیسک** — همهٔ دیسک‌ها با حجم و مدل و نوع اتصال؛ برای فعال شدن دکمه باید نامش را تایپ کنید | **۶ · نصب** — نوشتن با progress و لاگ زنده، بعد بزرگ‌کردن پارتیشن |

| | |
|---|---|
| ![دیسک، فارسی](docs/screenshots/07-disk-fa.png) | ![خوش‌آمد روی موبایل](docs/screenshots/08-welcome-mobile.png) |
| چیدمان راست‌به‌چپ با فونت DanaFaNum؛ نام دستگاه‌ها LTR می‌مانند | تک‌ستونه، بدون اسکرول افقی، همان تم |

## ساخت ISO

```bash
./build.sh --check   # ببینید روی این سیستم چه چیزی کم است
sudo ./build.sh      # می‌سازد: build/ezPeDitXOS-installer-amd64.hybrid.iso
```

`lb config` با کاربر عادی اجرا می‌شود. فقط `lb build` به root نیاز دارد، چون
`debootstrap` باید داخل chroot دستگاه‌ها را با `mknod` بسازد — user namespace
این کار را نمی‌تواند بکند، پس `unshare -r` جایگزین `sudo` نیست.

ابزارهای لازم روی میزبان (اگر `--no-deps` نداده باشید، `build.sh` خودش
نصبشان می‌کند): `debootstrap squashfs-tools xorriso mtools dosfstools rsync git`.

اگر `live-build` نصب‌شده نسخهٔ فعلی باشد از همان استفاده می‌شود؛ Ubuntu هنوز
فورک قدیمی `3.0~a57` را می‌دهد که در آن حالت اسکریپت نسخهٔ Debian را در
`.tools/` کلون می‌کند.

## ریختن روی USB

```bash
sudo dd if=build/ezPeDitXOS-installer-amd64.hybrid.iso of=/dev/sdX bs=4M status=progress conv=fsync
```

`/dev/sdX` خودِ استیک است، نه پارتیشن آن. تصویر isohybrid است: همان فایل هم
با BIOS قدیمی و هم با UEFI بوت می‌شود. **Secure Boot باید خاموش باشد** — هیچ
چیز امضاشده‌ای اینجا نیست.

## نحوهٔ استفاده

1. استیک را به دستگاه مقصد وصل کنید و از آن بوت کنید.
2. آدرس بعد از لاگین در کنسول چاپ می‌شود و در صفحهٔ وب هم هست. دستگاه یک
   DHCP client ساده است — هیچ IP ثابتی در کار نیست.
3. شش مرحله را طی کنید: **خوش‌آمد → تنظیمات → فایل → بررسی hash → دیسک → نصب**.
4. بعد از اتمام، استیک را بیرون بکشید و از دیسک تازه نوشته‌شده بوت کنید.

## چه چیزی داخل تصویر است

| | |
|---|---|
| پایه | Debian `trixie`، `iso-hybrid`، BIOS + UEFI |
| کرنل | `linux-image-amd64` (همهٔ درایور‌های شبکهٔ amd64 در یک تصویر) |
| فریمور | realtek، atheros، bnx2/bnx2x، qlogic، myricom، netxen، netronome، cavium، misc-nonfree، intel-misc — کامپوننت `non-free-firmware` |
| شبکه | NetworkManager، DHCP روی همهٔ کارت‌های اترنت |
| نصب‌کننده | `usr/local/sbin/ezpd` (Python 3، فقط کتابخانهٔ استاندارد) + `usr/local/share/ezpd/` |
| ابزار دیسک | `growpart`، `parted`، `gdisk`، `e2fsprogs` |

دو موردی که حجم را تعیین می‌کنند: `--firmware-chroot false` (وگرنه هر بستهٔ
`firmware-*` موجود در آرشیو کشیده می‌شود — خودش حدود ۱ گیگ است) و
`--apt-recommends false`. انتظار یک ISO در حدود **۴۵۰ تا ۶۰۰ مگابایت** را
داشته باشید.

## نکته‌های مرحلهٔ نوشتن

- **کل تصویر با `dd` نوشته می‌شود** — تصویر `*-combined-efi.img.gz` رسمی
  OpenWrt خودش GPT و GRUB برای هر دو حالت BIOS و UEFI دارد؛ پس `grub-install`
  لازم نیست.
- **`gzip` روی این تصاویر با کد ۲ خارج می‌شود** چون OpenWrt بعد از جریان gzip
  متادیتای `fwtool` را اضافه می‌کند. نصب‌کننده کد ۲ را صریحاً می‌پذیرد؛ بدون
  این کار، هر نوشتنِ **موفق** هم شکست خورده گزارش می‌شد.
- **مرحلهٔ بزرگ‌کردن**: اول `sgdisk -e` (این تصاویر backup GPT را وسط دیسک
  می‌فرستند، نه انتهایش)، بعد بزرگ کردن پارتیشن آخر. روی **squashfs** دیگر
  کاری لازم نیست — درایور `rootdisk`ِ OpenWrt دُم آزادشده را به overlay
  نوشتنی تبدیل می‌کند. روی **ext4** بعدش `resize2fs` می‌آید.
- **ایمنی**: هر دیسکی که چیزی روی سیستم زنده روی آن mount شده باشد قفل
  است — از جمله همان استیکی که ISO از آن بوت شده، و دیسک‌هایی که پشتصحنهٔ
  یک ایمیج mountشده‌اند (پس بوت کردن نصب‌کننده به‌صورت دیسک خام در VM هم
  امن است). دیسک‌های USB قابل جابجایی به override صریح نیاز دارند و باید
  نام دیسک مقصد را تایپ کنید تا دکمهٔ نصب فعال شود.
- نصب‌کننده هرگز IP ثابت نمی‌گذارد. هیچ‌جا `192.168.1.1` فرض نشده؛ هر چه DHCP
  بدهد همان را صفحه چاپ می‌کند.

## خودآزمایی

```bash
python3 payload/usr/local/sbin/ezpd --selftest
```

قواعد ایمنی دیسک، مدیریت مسیر، لوله‌کشی gzip با دنبالهٔ زائد، محاسبهٔ stream
شدهٔ sha256 و اعتبارسنجی ساعت/منطقهٔ زمانی را پوشش می‌دهد. root لازم ندارد.

## محدودیت‌ها

- **Secure Boot** پشتیبانی نمی‌شود (GRUB امضا نشده، shim هم نیست).
- **لوگوی بوت** best-effort است: تم Plymouth فقط وقتی نصب می‌شود که
  `script.so` موجود باشد، و در غیر این صورت رد می‌شود. بوت هرگز به آن وابسته
  نیست. بنر کنسول بعد از لاگین اما همیشه هست.
- روی سخت‌افزار واقعی هنوز تست نشده — تست بوت با QEMU قدم بعدی است.

## ساختار پوشه‌ها

```
build.sh                 ساخت ISO
payload/                 فایل‌هایی که روی فایل‌سیستم زنده کپی می‌شوند
  usr/local/sbin/ezpd               نصب‌کننده (API + پشتیبان ویزارد)
  usr/local/share/ezpd/index.html   ویزارد تک‌صفحه‌ای (fa/en)
  usr/local/share/ezpd/assets/      لوگو، favicon، فونت‌های Dana/Vazirmatn/Outfit
  etc/systemd/system/ezpd.service   اجرا هنگام بوت
  etc/profile.d/90-ezpd.sh          چاپ آدرس بعد از لاگین
docs/screenshots/        تصاویر ویزارد که در هر دو README استفاده شده‌اند
```

---

PeDitX توسط PeDitX — [peditx.ir](https://peditx.ir) · فریمور: [peditxos.ir](https://peditxos.ir)

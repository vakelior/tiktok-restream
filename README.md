# 🎬 TikTok Restream — إعادة بث m3u8 على RTMP بـ GitHub Actions

إعادة بث **قناة m3u8** على **RTMP** (Restream / TikTok / YouTube) باستعمال **FFmpeg** مع نسخ مباشر (`-c copy`) => استهلاك CPU شبه صفر.

---

## ⚠️ مهم قبل ما تبدأ

- GitHub Actions عندو **حد أقصى ~6 ساعات** لكل job، ومن بعد كيوقف. الـ cron كل ساعة كيبدى job جديد مرة أخرى، ولكن ما كاينش ضمان ديال استمرارية 24/7 بلا انقطاع.
- هاد الاستعمال **ماشي الحل الصحيح** لـ 24/7 دائم. للحل الدائم، استعمل VPS (Oracle Free Tier مجاني) — شوف آخر الصفحة.
- **stream key ديالك حساس** — خزّنو غير فـ GitHub Secrets، عمّرو ما تكتبو فالكود.

---

## 🚀 الإعداد (خطوة بخطوة)

### 1. وضَع الأسرار (Secrets)

روح لـ: **Settings → Secrets and variables → Actions → New repository secret**

| الاسم | القيمة |
|-------|--------|
| `M3U8_URL` | رابط القناة m3u8 (المصدر) |
| `RTMP_URL` | رابط RTMP كامل مع stream key (الوجهة) |

مثال على `RTMP_URL`:
```
rtmp://live.restream.io/live/re_XXXXXXX
```

### 2. شغّل الـ workflow

- **تلقائياً**: كيخدم كل ساعة عبر cron.
- **يدوياً**: من تبويب **Actions → Restream m3u8 to RTMP → Run workflow**.

---

## 🎯 قنوات m3u8 حية مجانية (للتجربة)

| القناة | الرابط |
|--------|--------|
| Mux (1080p/60fps) | `https://test-streams.mux.dev/x36xhzz/x36xhzz.m3u8` |
| iReplay Blender (24/7) | `https://ireplay.tv/test/blender.m3u8` |
| Akamai (Live) | `https://cph-p2p-msl.akamaized.net/hls/live/2000341/test/master.m3u8` |

---

## 🖥️ تشغيل محلي (على جهازك) بلا GitHub

```bash
sudo apt install ffmpeg
ffmpeg -re -i "https://example.com/stream.m3u8" -c copy -f flv "rtmp://..."
```

السكريبت ديال `restream.sh` كيتوفر فهاد الريبو مع auto-restart و logs.

---

## ☁️ الحل الصحيح لـ 24/7 (VPS)

```bash
# على سيرفر VPS (مثلاً Oracle Cloud Free Tier)
sudo apt install ffmpeg
nohup ffmpeg -re -i "M3U8_URL" -c copy -f flv "RTMP_URL" &
```

أو استعمل Docker:
```bash
docker run -d --restart unless-stopped \
  -e M3U8_URL="..." -e RTMP_URL="..." \
  jrottenberg/ffmpeg:7-alpine \
  -re -i "$M3U8_URL" -c copy -f flv "$RTMP_URL"
```

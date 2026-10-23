![Status](https://img.shields.io/badge/status-prototype-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Platform](https://img.shields.io/badge/platform-localhost-orange)

# KelimeVS CMS Prototype

> The original CMS prototype built for KelimeVS, later evolved into OpsBare.

## Overview

KelimeVS is an online multiplayer word puzzle game that stores its content in Firebase Realtime Database.

As the project grew, managing hundreds of questions, categories and alternative answers directly through the Firebase Console became increasingly inefficient. To solve this problem, I developed a lightweight localhost CMS dedicated to the game's content management.

Although originally designed specifically for KelimeVS, the ideas and architecture explored in this prototype eventually evolved into **OpsBare**—a standalone database management platform.

---

## Screenshots

<div align="center">
  <table border="0" style="border: none; border-collapse: collapse; width: 100%;">
    <tr style="border: none;">
      <td align="center" style="border: none; padding: 2px; width: 50%;">
        <img src="assets/dashboard.png" style="width: 100%; max-width: 100%; display: block;">
        <br>
        <b>Dashboard</b>
      </td>
      <td align="center" style="border: none; padding: 2px; width: 50%;">
        <img src="assets/questionpool.png" style="width: 100%; max-width: 100%; display: block;">
        <br>
        <b>Question Pool Window</b>
      </td>
    </tr>
  </table>
</div>

## Features

- Firebase Realtime Database integration
- Firebase Authentication (Email & Password administrator login)
- Automatic `content_version` synchronization with debounced cache bumping
- Question management & Alternative answer mapping
- Category management
- Full data import/export (JSON backup & selective export)
- Localhost-based lightweight administration panel

---

## Configuration & Security

### 1. Firebase Authentication
Bu panel kilitli Firebase Realtime Database yapıları için tasarlanmıştır:
- Panel arayüzünden **Firebase Ayarları** açılarak proje kimlik bilgileri girilir.
- Ardından yetkili yönetici hesabı (**E-posta + Parola**) ile giriş yapılır.
- Parola asla yerel depolamada (`localStorage`) tutulmaz; oturum güvenliği Firebase Auth'un `browserLocalPersistence` mekanizması ile sağlanır.

### 2. Realtime Database Güvenlik Kuralları (Örnek)
Veritabanını yalnızca yetkili yönetici hesabının yazıp okuyabilmesi için Firebase Realtime Database kurallarında yönetici UID'si kilitlenmelidir (örnek şablon):

```json
{
  "rules": {
    "questions_pool": {
      ".read": "auth != null && auth.uid === '<YONETICI_UID>'",
      ".write": "auth != null && auth.uid === '<YONETICI_UID>'"
    },
    "cms_categories": {
      ".read": "auth != null && auth.uid === '<YONETICI_UID>'",
      ".write": "auth != null && auth.uid === '<YONETICI_UID>'"
    },
    "content_version": {
      ".read": true,
      ".write": "auth != null && auth.uid === '<YONETICI_UID>'"
    }
  }
}
```
> **Not:** Mobil oyun istemcisi soru havuzunu ve kategorileri genel okuyorsa `.read` izinleri oyun mimarinize göre (`true` veya istemci auth kuralı ile) yapılandırılabilir. `content_version` alanı oyun istemcilerinin önbellek yenileme kontrolü yapabilmesi için genel okumaya (`true`) açık tutulabilir.

### 3. `content_version` ve Oyun Önbelleği
- CMS paneli üzerinden yapılan soru ekleme, düzenleme, silme, taşıma ve toplu içe aktarma işlemlerinde veritabanındaki `content_version` düğümü otomatik olarak güncel zaman damgasıyla (`Date.now()`) güncellenir.
- Hızlı art arda yapılan düzenlemelerde sunucuya gereksiz yazma maliyetini önlemek amacıyla **1.5 saniyelik debounce** kullanılır.
- Üst menüde (Header) yer alan **"Oyuna Yansıt"** butonu kullanılarak sürüm güncellemesi dilediğiniz an manuel ve anlık olarak tetiklenebilir.

---

## Project Evolution

```text
KelimeVS
     │
     ▼
KelimeVS CMS Prototype
     │
     ▼
OpsBare
```

This repository represents the original prototype that inspired the development of OpsBare.

---

## Status

This project is no longer under active development.

It remains available as an open-source reference that documents the early development process behind [OpsBare](https://github.com/RieStudio/OpsBare).

---

## License

This project is licensed under the [MIT License](LICENSE).

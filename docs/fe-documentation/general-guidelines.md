---
sidebar_position: 3
---

# General Guidlines

Berikut adalah pedoman penulisan kode dan dokumentasi pada code untuk menjaga konsistensi, keterbacaan, dan kualitas dalam proyek antara beberapa developer dan developer onborading.

## Gaya Penulisan (Writing Styles)

Dalam penulisan code dianjurkan menggunakan bahasa inggris, dari penulisan function, variable agar memberikan konsistensi antara para developer yang lainnya.

- **Bahasa yang digunakan**: Pengkodean diwajibkan menggunakan bahasa Inggris.
- **Gaya bahasa**: 
  - Gunakan kalimat aktif untuk menjelaskan tindakan.
  - Hindari jargon teknis yang tidak diperlukan.

**Contoh:**
```md
### Poor:
Button is clicked when the user wants to do something.

### Better:
The button triggers an action when clicked by the user.
```

## Format Kode (Code Formating)

Code formating menjadi sangat penting ketika kita melakukan colaborasi dengan beberapa developer. Kita memberikan dokumentasi yang harus dilaksanakan dan diperhatikan.

**- Indentasi:**
- Gunakan indentasi 2 spasi.

**Contoh:**
```js
// Indentasi 2 spasi
function greet() {
  console.log("Hello, World!");
}
```

**- Penulisan Code:**
 - Gunakan **camelCase** untuk variabel dan fungsi.
 - Gunakan **PascalCase** untuk komponen React atau Vue.

```js
const userProfile = { name: "Alice" };
function getUserName(userProfile) {
  return userProfile.name;
}
```

**- Penulisan Code:**
 - Gunakan komentar untuk menjelaskan bagian kode yang kompleks.

```js
// Fungsi untuk menghitung diskon berdasarkan harga asli
function calculateDiscount(price, discountRate) {
  return price * (1 - discountRate);
}
```

Bagian ini memberikan pedoman dasar yang menjadi fondasi konsistensi untuk semua kode dan dokumentasi dalam proyek frontend.








---
sidebar_position: 4
---
# File Structure

Bagian ini menjelaskan struktur file dan folder yang digunakan dalam proyek frontend. Tujuannya adalah untuk memastikan bahwa semua pengembang memahami di mana menempatkan file, bagaimana file diorganisasi, dan bagaimana mereka saling terkait. Struktur yang jelas memudahkan pengelolaan, pencarian, dan penambahan fitur baru.

**Requirements:**
- NodeJS : Menggunakan NodeJS 20 or latest
- PNPM : Menggunakan versi pnpm 9 or latest
- Components UI : [shadcn/ui](https://ui.shadcn.com/). 
- CSS Library: [TailwindCSS](https://tailwindcss.com/docs/installation)
- Icons Library: [Lucide Icons](https://lucide.dev/icons/)

<mark>**WAJIB MENGGUNAKAN TYPESCRIPT**</mark>

### Directory Structure

- Menyajikan struktur folder utama dalam proyek.
- Memberikan deskripsi singkat untuk setiap folder atau sub-folder.

**Structure project in NextJS:**
```plaintext
.
├── Dockerfile
├── README.md
├── _app
│   ├── _modules
│   │   ├── auth-signin
│   │   │   ├── index.tsx
│   │   │   └── types.d.ts
│   │   ├── bo-dashboard
│   │   │   └── index.tsx
│   │   └── user-landing
│   │       └── index.tsx
│   ├── components
│   │   ├── _layouts
│   │   │   ├── Layout
│   │   │   │   ├── index.tsx
│   │   │   │   └── types.d.ts
│   │   │   └── layout-admin
│   │   │       ├── admin-menu.ts
│   │   │       ├── index.tsx
│   │   │       ├── navbar.tsx
│   │   │       ├── sidebar.tsx
│   │   │       └── types.d.ts
│   │   ├── atoms
│   │   │   ├── NoSsr
│   │   │   │   └── index.tsx
│   │   │   └── show
│   │   │       ├── index.tsx
│   │   │       └── types.d.ts
│   │   ├── molecules
│   │   ├── organisms
│   │   └── ui
│   ├── config
│   │   ├── env.ts
│   │   └── routes.ts
│   ├── helpers
│   │   ├── cookies.ts
│   │   ├── currency.ts
│   │   ├── trans.ts
│   │   └── utils.ts
│   ├── hooks
│   │   ├── http.ts
│   │   ├── useEnchancedEffect.ts
│   │   └── useToast.ts
│   └── models
│   └── services
│       ├── local
│       │   └── repository
│       ├── remote
│       │   └── repository
│       └── state
│           └── repository
├── auto-imports.d.ts
├── components.json
├── docker-compose.yml
├── locales
│   ├── en.json
│   └── id.json
├── next.config.js
├── package.json
├── pnpm-lock.yaml
├── postcss.config.js
├── public
│   ├── next.svg
│   └── vercel.svg
├── src
│   ├── app
│   │   ├── (private)
│   │   │   └── bo
│   │   │       ├── dashboard
│   │   │       │   └── page.tsx
│   │   │       └── layout.tsx
│   │   ├── (public)
│   │   │   ├── auth
│   │   │   │   └── signin
│   │   │   │       ├── layout.tsx
│   │   │   │       └── page.tsx
│   │   │   └── page.tsx
│   │   ├── favicon.ico
│   │   ├── globals.css
│   │   ├── layout.tsx
│   │   └── providers.tsx
│   ├── i18n
│   │   ├── request.ts
│   │   └── routing.ts
│   └── middleware.ts
├── tailwind.config.ts
├── tsconfig.json
└── tsconfig.paths.json
```

**Penjelasan:**

- `` _app/modules``: Berisi tiap-tiap modules pada setiap halaman
- `` _app/assets``: Berisi assets statuc yang ada pada project dan harus dikelompokkan
- ``_app/components``: Berisi components frontend yang reuseable
  - ``_app/components/_layouts``: Berisi components layout untuk menentukan layout halaman.
  - ``_app/components/atoms``: Berisi components terkecil jika ingin memperkecil sebuah components yang reuseable
  - ``_app/components/molecules``: Berisi components yang lebih besar dari components atoms.
  - ``_app/components/organisms``: Berisi components yang teroragnisasi, seperti input select kota, select provinsi, dll.
  - ``_app/components/ui``: Berisi components dari shadcn/ui.
  - ``_app/config``: Berisi config dari aplikasi yang berisi file ``env.ts`` dan ``routes.ts``
  - ``_app/helpers``: Berisi helpers dari aplikasi.
  - ``_app/hooks``: Berisi hooks yang dibutuhkan aplikasi.
  - ``_app/models``: Berisi models dari response API atau types data. Pisahkan menjadi tiap tiap module.
  - ``_app/services``: Berisi services untuk bisnis dev
    - ``_app/services/local``: Digunakan untuk menyimpan data localstorage, cookies, dll.
    - ``_app/services/remote``: Digunakan untuk menyimpan data request ke API Backend
    - ``_app/services/state``: Digunakan untuk menyimpan store
  - ``public/``: Public folder digunakan untuk menyimpan file agar dapat diakses secara public
  - ``src/app``: Source dari halaman
    - ``src/app/(private)``: Page untuk private halaman yang membutuhkan login
    - ``src/app/(public)``: Page untuk public halaman yang tidak membutuhkan login

### Penamaan File

Penentuan penamaan file untuk template ini

- **File dan Folder:**
  - Gunakan kebab-case untuk file dan folder biasa (contoh: header-component.ts).
  - Gunakan PascalCase untuk komponen folder

Note: Untuk membuat sebuah components gunakan folder dan gunakan file index.tsx.

**contoh:**
```
├── Empty
│   └── index.tsx
```

Bagian ini membantu memastikan bahwa struktur proyek tetap rapi, teratur, dan mudah dikelola, terutama ketika proyek berkembang lebih kompleks.


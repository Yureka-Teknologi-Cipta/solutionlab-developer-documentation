---
sidebar_position: 5
---

# TypeScript Guidelines

Bagian ini menjelaskan standar dan praktik terbaik untuk penulisan kode TypeScript dalam proyek frontend. Tujuannya adalah untuk memastikan kode yang ditulis konsisten, efisien, dan mudah dipahami oleh semua anggota tim.

### Code Style (Gaya Penulisan Kode)

Gaya penulisan kode menentukan format dan aturan dasar untuk menjaga konsistensi dalam penulisan kode.

- **Linting:**
	-	Gunakan alat seperti **ESLint** untuk memastikan gaya kode konsisten.
	-	Contoh aturan linting:
	  -	Indentasi: 2 spasi.
	  -	Kutipan: Gunakan tanda kutip tunggal (') untuk string.
	  -	Akhiran baris: Tambahkan titik koma (;).

- **Best Practices:**
	-	Gunakan const untuk variabel yang tidak berubah, dan let untuk variabel yang dapat berubah.
	-	Hindari penggunaan var.
  - Contoh : 
```ts
// Good:
const PI = 3.14;
let count = 0;

// Bad:
var name = "John";
```

- **Arrow Functions:**
	- Gunakan arrow function untuk fungsi anonim, kecuali jika membutuhkan konteks this.
	- Contoh:
```ts
// Good:
const add = (a, b) => a + b;

// Bad:
function add(a, b) {
  return a + b;
}
```

- Hooks:
	-	Gunakan React Hooks untuk state management, efek samping, dan logika lain.
	-	Contoh:

```tsx
import { useState } from 'react';

const Counter = () => {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
};
```

### Error Handling

Pastikan kode menangani error dengan baik untuk menghindari bug yang sulit ditemukan.

- **Try-Catch:**
	- Gunakan blok try-catch untuk menangani error pada operasi asinkron.
	- Contoh:

```ts
const fetchData = async () => {
  try {
    const response = await fetch('/api/data');
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error('Error fetching data:', error);
  }
};
```

### TypeScript

- **Gunakan Tipe Data yang Tepat:**
	- Hindari penggunaan tipe any kecuali benar-benar diperlukan.
	- Contoh:
```ts
interface User {
  id: number;
  name: string;
}

const getUser = (id: number): User => {
  return { id, name: 'John' };
};
```

- **Strict Mode:**
	- Aktifkan strict mode di file konfigurasi tsconfig.json untuk memastikan tipe data lebih ketat.
	- Contoh:
```ts
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true
  }
}
```

Bagian ini memberikan kerangka kerja yang kuat untuk menulis kode TypeScript yang konsisten, aman, dan terstruktur.
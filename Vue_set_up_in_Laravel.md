Step 1: Laravel project ready কিনা check

Project root এ terminal খুলে রান করো 👇
```bash
php artisan --version
node -v
npm -v
```
Node + npm না থাকলে আগে install করতে হবে।

Step 2: Frontend dependency install করো

Laravel 9+ / 10 / 11 / 12 — সবগুলোর জন্য same
```bash
npm install
```
Step 3: Vue install করো (Laravel এর ভিতর)
```bash
npm install vue@3
```

Step 4: Vite এ Vue plugin add কর
```bash
npm install @vitejs/plugin-vue
```

Step 5: vite.config.js update কর
```bash
import { defineConfig } from 'vite'
import laravel from 'laravel-vite-plugin'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
        vue(),
    ],
})
```

Step 6: Vue folder structure বানাও
```bash
resources/js/
│
├── app.js
├── bootstrap.js
├── components/
│   └── App.vue
└── pages/   (optional)
```

Step 7: app.js এ Vue initialize কর
```bash
import './bootstrap'
import { createApp } from 'vue'
import App from './components/App.vue'

createApp(App).mount('#app')
```

Step 8: App.vue বানাও

📄 resources/js/components/App.vue
```bash
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <h1>Laravel + Vue 🎉</h1>
  <p>Count: {{ count }}</p>
  <button @click="count++">+</button>
</template>

<style scoped>
h1 {
  color: #42b883;
}
</style>
```
Step 9: Blade file এ Vue mount কর

📄 resources/views/welcome.blade.php
```bash



<!DOCTYPE html>
<html>
<head>
    <title>Laravel Vue</title>
    @vite(['resources/js/app.js'])
</head>
<body>
    <div id="app"></div>
</body>
</html>


⚠️ id="app" must match .mount('#app')

```

Step 10: Run everything
```bash
Terminal 1:

php artisan serve


Terminal 2:

npm run dev

```
Browser এ open করো:
👉 http://127.0.0.1:8000

# mydiscordbot
My Discord Bot


Menciptakan dan mengembangkan bot GitHub yang memposting notifikasi event ke Discord adalah proyek menarik yang melibatkan beberapa langkah. Kita akan menggunakan GitHub Webhooks, Discord Webhooks, dan Node.js untuk membangun bot ini. Berikut adalah langkah-langkah terperinci untuk menciptakan dan mengembangkan bot tersebut:

### Langkah 1: Membuat Discord Webhook

1. **Masuk ke Discord**:
   - Buka server Discord Anda dan navigasi ke saluran tempat Anda ingin bot mengirim notifikasi.

2. **Membuat Webhook**:
   - Klik pada ikon roda gigi di sebelah saluran untuk membuka pengaturan saluran.
   - Pilih tab "Integrations" dan kemudian klik "Create Webhook".
   - Berikan nama untuk webhook Anda dan salin URL webhook yang dihasilkan.

### Langkah 2: Mengatur GitHub Webhook

1. **Masuk ke GitHub**:
   - Buka repositori GitHub Anda di mana Anda ingin mengatur webhook.

2. **Menambahkan Webhook**:
   - Klik pada tab "Settings" di repositori.
   - Pilih "Webhooks" dari menu sebelah kiri dan klik "Add webhook".
   - Masukkan URL server Anda yang akan menerima payload dari GitHub (nanti kita buat server ini menggunakan Node.js).
   - Pilih tipe konten sebagai `application/json`.
   - Pilih event yang ingin Anda terima (misalnya, `push` atau `pull_request`).
   - Klik "Add webhook".

### Langkah 3: Mengembangkan Server Node.js untuk Menerima Payload dari GitHub dan Mengirim ke Discord

1. **Inisialisasi Proyek Node.js**:
   - Buat direktori baru untuk proyek Anda dan inisialisasi proyek Node.js:
     ```bash
     mkdir github-to-discord-bot
     cd github-to-discord-bot
     npm init -y
     ```

2. **Instalasi Dependencies**:
   - Instal dependencies yang diperlukan seperti `express` untuk server dan `axios` untuk mengirim request ke Discord:
     ```bash
     npm install express axios body-parser
     ```

3. **Membuat File Server**:
   - Buat file baru bernama `server.js` dan tambahkan kode berikut:
     ```javascript
     const express = require('express');
     const bodyParser = require('body-parser');
     const axios = require('axios');

     const app = express();
     const PORT = process.env.PORT || 3000;
     const DISCORD_WEBHOOK_URL = 'YOUR_DISCORD_WEBHOOK_URL'; // Ganti dengan URL webhook Discord Anda

     app.use(bodyParser.json());

     app.post('/github-webhook', async (req, res) => {
         const payload = req.body;

         try {
             let message = '';

             if (payload.pusher) {
                 message = `New push by ${payload.pusher.name} in repository ${payload.repository.name}:\n${payload.compare}`;
             } else if (payload.pull_request) {
                 message = `New pull request by ${payload.pull_request.user.login} in repository ${payload.repository.name}:\n${payload.pull_request.html_url}`;
             } else {
                 message = `Event received: ${JSON.stringify(payload)}`;
             }

             await axios.post(DISCORD_WEBHOOK_URL, {
                 content: message
             });

             res.status(200).send('Event received and posted to Discord');
         } catch (error) {
             console.error('Error posting to Discord:', error);
             res.status(500).send('Error processing event');
         }
     });

     app.listen(PORT, () => {
         console.log(`Server is running on port ${PORT}`);
     });
     ```

4. **Menjalankan Server**:
   - Jalankan server menggunakan Node.js:
     ```bash
     node server.js
     ```

### Langkah 4: Menyebarkan Server (Opsional)

Jika Anda ingin server ini selalu online, Anda bisa menggunakan layanan cloud seperti Heroku, AWS, atau lainnya untuk menyebarkan aplikasi Node.js Anda.

### Langkah 5: Uji Coba

1. **Testing GitHub Webhook**:
   - Buat perubahan di repositori GitHub Anda untuk memicu event (misalnya, push perubahan baru atau membuat pull request).
   - Periksa log di server Node.js Anda untuk memastikan bahwa payload diterima.

2. **Verifikasi di Discord**:
   - Buka saluran Discord Anda dan pastikan pesan notifikasi dari GitHub muncul sesuai dengan event yang terjadi.

Dengan mengikuti langkah-langkah di atas, Anda akan berhasil menciptakan dan mengembangkan bot GitHub yang memposting notifikasi event ke Discord. Anda dapat memperluas dan menyesuaikan bot ini sesuai dengan kebutuhan spesifik proyek Anda.

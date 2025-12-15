<template>
  <div
    class="min-h-screen bg-gradient-to-br from-slate-900 via-slate-800 to-slate-900 text-slate-100 flex items-center justify-center p-6"
  >
    <div class="relative w-full max-w-3xl">
      <div class="absolute inset-0 bg-gradient-to-r from-blue-500/20 via-cyan-400/10 to-indigo-500/20 blur-3xl rounded-3xl"></div>

      <div
        class="relative w-full max-w-2xl mx-auto backdrop-blur-xl bg-white/10 border border-white/20 shadow-2xl rounded-2xl p-8"
      >
        <div class="flex items-center justify-between mb-6">
          <div>
            <p class="text-sm uppercase tracking-[0.3em] text-slate-300">Media Lab</p>
            <h2 class="text-2xl font-semibold mt-1">Image Converter</h2>
            <p class="text-sm text-slate-300">Ubah format gambar dengan cepat & elegan.</p>
          </div>
          <div class="h-12 w-12 rounded-full bg-gradient-to-br from-blue-500 to-cyan-400 flex items-center justify-center text-white shadow-lg shadow-cyan-500/30">
            <span class="text-lg font-semibold">IC</span>
          </div>
        </div>

        <!-- Error Message -->
        <div
          v-if="errorMessage"
          class="mb-4 px-4 py-3 rounded-xl bg-red-500/20 border border-red-500/50 text-red-200 text-sm"
        >
          <strong>Error:</strong> {{ errorMessage }}
        </div>

        <!-- Success Message -->
        <div
          v-if="successMessage"
          class="mb-4 px-4 py-3 rounded-xl bg-emerald-500/20 border border-emerald-500/50 text-emerald-200 text-sm"
        >
          {{ successMessage }}
        </div>

        <div class="grid md:grid-cols-2 gap-6">
          <div class="flex flex-col gap-4">
            <label class="text-sm text-slate-200">Pilih Gambar</label>
            <label
              class="flex items-center justify-between gap-3 px-4 py-3 rounded-xl border border-white/20 bg-white/5 hover:bg-white/10 transition cursor-pointer"
            >
              <div class="flex flex-col flex-1 min-w-0">
                <span class="text-sm font-medium truncate">
                  {{ file ? file.name : "Unggah file (PNG, JPG, WEBP, ICO)" }}
                </span>
                <span class="text-xs text-slate-300">
                  {{ file ? formatFileSize(file.size) : "Maksimal 10MB" }}
                </span>
              </div>
              <span class="px-3 py-1 rounded-lg bg-gradient-to-r from-blue-500 to-cyan-400 text-xs font-semibold text-white whitespace-nowrap">
                Browse
              </span>
              <input
                type="file"
                accept="image/png,image/jpeg,image/jpg,image/webp,image/x-icon"
                @change="onFileChange"
                class="hidden"
              />
            </label>

            <div class="flex flex-col gap-2">
              <label class="text-sm text-slate-200">Format Tujuan</label>
              <select
                v-model="format"
                class="bg-white/5 border border-white/20 rounded-xl px-4 py-3 text-sm focus:outline-none focus:ring-2 focus:ring-cyan-400/60"
              >
                <option value="png">PNG</option>
                <option value="jpg">JPG</option>
                <option value="webp">WEBP</option>
                <option value="ico">ICO</option>
              </select>
            </div>

            <button
              @click="convert"
              :disabled="loading || !file"
              class="inline-flex items-center justify-center gap-2 bg-gradient-to-r from-blue-500 to-cyan-400 text-white px-4 py-3 rounded-xl text-sm font-semibold shadow-lg shadow-cyan-500/30 disabled:opacity-50 disabled:cursor-not-allowed transition hover:shadow-cyan-500/50"
            >
              <span v-if="loading" class="h-4 w-4 rounded-full border-2 border-white/60 border-t-transparent animate-spin"></span>
              <span>{{ loading ? "Memproses..." : "Convert Sekarang" }}</span>
            </button>
          </div>

          <div class="flex flex-col gap-4">
            <div class="rounded-xl border border-white/20 bg-white/5 p-4 min-h-[160px] flex items-center justify-center text-center">
              <div v-if="downloadUrl" class="space-y-3 w-full">
                <p class="text-sm text-slate-200">✓ Konversi berhasil!</p>
                <button
                  @click="downloadFile"
                  class="inline-flex items-center gap-2 px-4 py-2 rounded-lg bg-white/10 border border-white/20 text-sm font-semibold hover:bg-white/15 transition"
                >
                  <span class="h-2 w-2 rounded-full bg-emerald-400 animate-pulse"></span>
                  Download {{ filename || 'Hasil' }}
                </button>
                <p class="text-xs text-slate-400">
                  File akan terhapus otomatis dalam 60 detik
                </p>
              </div>
              <div v-else-if="loading" class="text-sm text-slate-300">
                <div class="animate-pulse">Sedang memproses gambar...</div>
              </div>
              <div v-else class="text-sm text-slate-300">
                Hasil akan muncul di sini setelah proses konversi selesai.
              </div>
            </div>

            <div class="rounded-xl border border-white/20 bg-white/5 p-4 text-xs text-slate-300 leading-relaxed">
              <p class="font-semibold text-slate-200 mb-2">💡 Tips kualitas</p>
              <ul class="list-disc list-inside space-y-1">
                <li>PNG cocok untuk transparansi.</li>
                <li>JPG untuk ukuran file kecil.</li>
                <li>WEBP seimbang antara kualitas & ukuran.</li>
                <li>ICO untuk favicon atau ikon aplikasi.</li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      file: null,
      format: "png",
      downloadUrl: "",
      filename: "",
      loading: false,
      errorMessage: "",
      successMessage: "",
      backendUrl: import.meta.env.VITE_BACKEND_URL || "http://localhost:5000",
      apiKey: import.meta.env.VITE_API_KEY || ""
    };
  },

  methods: {
    onFileChange(e) {
      const selectedFile = e.target.files[0];
      
      if (!selectedFile) return;

      // Validasi ukuran file (10MB)
      const maxSize = 10 * 1024 * 1024;
      if (selectedFile.size > maxSize) {
        this.errorMessage = "File terlalu besar! Maksimal 10MB.";
        this.file = null;
        e.target.value = "";
        return;
      }

      // Validasi tipe file
      const allowedTypes = ['image/png', 'image/jpeg', 'image/jpg', 'image/webp', 'image/x-icon'];
      if (!allowedTypes.includes(selectedFile.type)) {
        this.errorMessage = "Format file tidak didukung! Gunakan PNG, JPG, WEBP, atau ICO.";
        this.file = null;
        e.target.value = "";
        return;
      }

      this.file = selectedFile;
      this.downloadUrl = "";
      this.filename = "";
      this.errorMessage = "";
      this.successMessage = "";
    },

    formatFileSize(bytes) {
      if (bytes === 0) return '0 Bytes';
      const k = 1024;
      const sizes = ['Bytes', 'KB', 'MB'];
      const i = Math.floor(Math.log(bytes) / Math.log(k));
      return Math.round(bytes / Math.pow(k, i) * 100) / 100 + ' ' + sizes[i];
    },

    async convert() {
      if (!this.file) return;

      this.loading = true;
      this.downloadUrl = "";
      this.filename = "";
      this.errorMessage = "";
      this.successMessage = "";

      const form = new FormData();
      form.append("file", this.file);
      form.append("format", this.format);

      try {
        const res = await fetch(`${this.backendUrl}/api/convert`, {
          method: "POST",
          headers: {
            "X-API-Key": this.apiKey
          },
          body: form
        });

        if (!res.ok) {
          const errorData = await res.json().catch(() => ({ error: "Unknown error" }));
          throw new Error(errorData.error || `Server error: ${res.status}`);
        }

        const data = await res.json();

        if (data.download_url && data.filename) {
          this.filename = data.filename;
          // Simpan URL lengkap untuk download
          const base = this.backendUrl.replace(/\/$/, "");
          this.downloadUrl = `${base}${data.download_url}`;
          this.successMessage = "Konversi berhasil! Silakan download file Anda.";
        } else {
          throw new Error("Invalid response from server");
        }

      } catch (err) {
        console.error("Conversion error:", err);
        this.errorMessage = err.message || "Terjadi kesalahan saat konversi. Silakan coba lagi.";
      } finally {
        this.loading = false;
      }
    },

    async downloadFile() {
      if (!this.downloadUrl) return;

      try {
        const res = await fetch(this.downloadUrl, {
          method: "GET",
          headers: {
            "X-API-Key": this.apiKey
          }
        });

        if (!res.ok) {
          throw new Error("Download failed");
        }

        // Download file
        const blob = await res.blob();
        const url = window.URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = this.filename || 'converted-image';
        document.body.appendChild(a);
        a.click();
        
        // Cleanup
        window.URL.revokeObjectURL(url);
        document.body.removeChild(a);

        this.successMessage = "File berhasil diunduh!";

      } catch (err) {
        console.error("Download error:", err);
        this.errorMessage = "Gagal mengunduh file. Silakan coba lagi.";
      }
    }
  }
};
</script>

<style scoped>
/* Optional: Add smooth transitions */
button:hover:not(:disabled) {
  transform: translateY(-1px);
}
</style>
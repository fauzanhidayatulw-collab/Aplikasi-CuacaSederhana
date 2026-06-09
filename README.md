# Aplikasi-CuacaSederhana
Aplikasi cuaca sederhana
# ==========================================
# [1] INSTALASI LIBRARY
# ==========================================
!pip install requests -q


# ==========================================
# [2] IMPORT LIBRARY
# ==========================================
import requests
import ipywidgets as widgets
from IPython.display import display, HTML, clear_output


# ==========================================
# [3] CLASS WEATHER APP
#     - Konfigurasi API
#     - Fungsi ambil data cuaca dari OpenWeatherMap
#     - Fungsi format tampilan hasil cuaca (HTML)
# ==========================================
class WeatherApp:

    # --- Konfigurasi API ---
    def __init__(self):
        self.api_key = "9e82b44f80ed08027713237ce215001a"
        self.base_url = "http://api.openweathermap.org/data/2.5/weather"
        self.icon_url = "http://openweathermap.org/img/wn/"

    # --- Ambil data cuaca dari API ---
    def get_weather(self, city):
        try:
            params = {'q': city, 'appid': self.api_key, 'units': 'metric', 'lang': 'id'}
            response = requests.get(self.base_url, params=params)
            return response.json() if response.status_code == 200 else None
        except:
            return None

    # --- Format hasil cuaca menjadi tampilan HTML ---
    def format_hasil(self, data):
        if not data:
            return "<h3 style='color:red; text-align:center;'>❌ Kota tidak ditemukan!</h3>"

        name       = data['name']
        country    = data['sys']['country']
        temp       = data['main']['temp']
        feels_like = data['main']['feels_like']
        description= data['weather'][0]['description'].title()
        humidity   = data['main']['humidity']
        wind_speed = data['wind']['speed']
        pressure   = data['main']['pressure']
        icon       = data['weather'][0]['icon']

        return f"""
        <div style="max-width:420px; margin:20px auto; padding:28px;
             background:linear-gradient(135deg,#667eea 0%,#764ba2 100%);
             border-radius:24px; color:white; font-family:sans-serif; box-shadow:0 8px 32px rgba(0,0,0,.25);">
            <h2 style="text-align:center; margin:0 0 4px;">📍 {name}, {country}</h2>
            <div style="text-align:center; margin-bottom:16px;">
                <img src="{self.icon_url}{icon}@4x.png" width="90">
                <div style="font-size:3em; font-weight:900; margin:4px 0;">{temp:.1f}°C</div>
                <div style="font-size:1.1em; opacity:.9;">{description}</div>
                <div style="font-size:.85em; opacity:.65;">Terasa seperti {feels_like:.1f}°C</div>
            </div>
            <div style="display:grid; grid-template-columns:1fr 1fr; gap:12px;">
                <div style="background:rgba(255,255,255,.15); border-radius:16px; padding:14px; text-align:center;">
                    💧 Kelembaban<br><strong style="font-size:1.2em;">{humidity}%</strong>
                </div>
                <div style="background:rgba(255,255,255,.15); border-radius:16px; padding:14px; text-align:center;">
                    💨 Angin<br><strong style="font-size:1.2em;">{wind_speed} m/s</strong>
                </div>
                <div style="background:rgba(255,255,255,.15); border-radius:16px; padding:14px; text-align:center;">
                    🌡️ Tekanan<br><strong style="font-size:1.2em;">{pressure} hPa</strong>
                </div>
            </div>
        </div>
        """


# ==========================================
# [4] INISIALISASI APP & OUTPUT UTAMA
# ==========================================
app = WeatherApp()
main_output = widgets.Output()


# ==========================================
# [5] HOME SCREEN
#     - Tampilan awal aplikasi
#     - Tombol "Mulai Cari" untuk masuk ke halaman pencarian
# ==========================================

# --- Tampilan kartu home ---
home_html = widgets.HTML("""
    <div style="max-width:420px; margin:0 auto; text-align:center; padding:48px 32px;
         background:#f5f5f5; border-radius:24px; font-family:sans-serif;">
        <div style="font-size:5em; margin-bottom:8px;">🌤️</div>
        <h2 style="margin:0 0 8px; font-size:1.8em; font-weight:bold; color:#000;">Aplikasi Cuaca</h2>
        <p style="color:#555; margin:0 0 8px;">Cek cuaca kota mana pun di dunia</p>
        <p style="color:#000; font-size:.9em;">Klik tombol di bawah untuk memulai</p>
    </div>
""")

# --- Tombol mulai ---
btn_mulai = widgets.Button(
    description="Mulai Cari 🔍",
    button_style='success',
    layout=widgets.Layout(width='200px', height='48px', margin='16px auto 0 auto')
)
btn_mulai.style.button_color = '#7c3aed'
btn_mulai.style.font_weight = 'bold'

# --- Susun home screen ---
home_screen = widgets.VBox(
    [home_html, btn_mulai],
    layout=widgets.Layout(align_items='center')
)


# ==========================================
# [6] SEARCH SCREEN
#     - Input nama kota
#     - Tombol "Cek Cuaca" (disembunyikan setelah hasil muncul)
#     - Area output hasil cuaca
#     - Tombol "Kembali" ke home
# ==========================================

# --- Judul halaman pencarian ---
search_title = widgets.HTML("""
    <div style="max-width:420px; margin:0 auto; text-align:center; font-family:sans-serif;">
        <h2 style="margin:0 0 6px;">🔍 Cari Kota</h2>
        <p style="color:#666; margin:0;">Masukkan nama kota yang ingin dicek cuacanya.</p>
    </div>
""")

# --- Input teks nama kota ---
txt_kota = widgets.Text(
    placeholder="Contoh: Jakarta, Tokyo, London...",
    layout=widgets.Layout(width='360px', height='48px')
)

# --- Tombol cek cuaca ---
btn_cari = widgets.Button(
    description="Cek Cuaca 🌍",
    button_style='info',
    layout=widgets.Layout(width='360px', height='48px', border='none')
)
btn_cari.style.button_color = '#7c3aed'
btn_cari.style.font_weight = 'bold'

# --- CSS: buat sudut tombol lebih membulat ---
btn_cari_style = widgets.HTML("""
<style>
  .widget-button {
    border-radius: 12px !important;
  }
</style>
""")

# --- Tombol kembali ke home ---
btn_kembali = widgets.Button(
    description="← Kembali",
    button_style='',
    layout=widgets.Layout(width='120px', height='36px')
)

# --- Area output hasil cuaca ---
result_output = widgets.Output()

# --- Susun search screen ---
search_screen = widgets.VBox(
    [search_title, txt_kota, btn_cari, result_output, btn_kembali],
    layout=widgets.Layout(
        align_items='center',
        display='none',
        grid_gap='12px'
    )
)


# ==========================================
# [7] FUNGSI NAVIGASI
#     - show_home   : kembali ke home screen
#     - show_search : masuk ke search screen
# ==========================================
def show_home():
    home_screen.layout.display   = 'flex'
    search_screen.layout.display = 'none'
    txt_kota.value = ''
    btn_cari.layout.display = 'flex'  # reset visibilitas tombol
    with result_output:
        clear_output()

def show_search():
    home_screen.layout.display   = 'none'
    search_screen.layout.display = 'flex'


# ==========================================
# [8] FUNGSI PENCARIAN CUACA
#     - do_search      : ambil & tampilkan hasil, sembunyikan tombol
#     - on_input_change: tampilkan tombol kembali saat user mengetik
# ==========================================
def do_search():
    kota = txt_kota.value.strip()
    with result_output:
        clear_output()
        if kota:
            display(HTML("<p style='text-align:center;color:#888;'>Mengambil data cuaca…</p>"))
            data = app.get_weather(kota)
            clear_output()
            display(HTML(app.format_hasil(data)))
            btn_cari.layout.display = 'none'  # sembunyikan tombol setelah hasil muncul
        else:
            display(HTML("<p style='color:red; text-align:center;'>❌ Masukkan nama kota!</p>"))

def on_input_change(change):
    if change['name'] == 'value':
        btn_cari.layout.display = 'flex'  # tampilkan tombol saat mengetik lagi


# ==========================================
# [9] HANDLER TOMBOL & EVENT
#     - on_mulai   : klik tombol "Mulai Cari"
#     - on_kembali : klik tombol "← Kembali"
#     - on_cari    : klik tombol "Cek Cuaca"
#     - on_enter   : (opsional) submit via Enter
# ==========================================
def on_mulai(b):
    show_search()

def on_kembali(b):
    show_home()

def on_cari(b):
    do_search()

def on_enter(change):
    if change['name'] == 'value' and change['new'].endswith('\n'):
        txt_kota.value = change['new'].strip()
        do_search()

# --- Daftarkan observer & event handler ---
txt_kota.observe(on_input_change)
btn_mulai.on_click(on_mulai)
btn_cari.on_click(on_cari)
btn_kembali.on_click(on_kembali)


# ==========================================
# [10] JAVASCRIPT: HANDLE TOMBOL ENTER
#      Memungkinkan user menekan Enter di input
#      untuk langsung menjalankan pencarian
# ==========================================
enter_js = widgets.HTML("""
<script>
(function() {
  function attachEnter() {
    var inputs = document.querySelectorAll('.widget-text input');
    inputs.forEach(function(inp) {
      if (inp._enterAttached) return;
      inp._enterAttached = true;
      inp.addEventListener('keydown', function(e) {
        if (e.key === 'Enter') {
          var btns = document.querySelectorAll('.widget-button');
          btns.forEach(function(btn) {
            if (btn.textContent.trim().startsWith('Cek Cuaca')) btn.click();
          });
        }
      });
    });
  }
  var tries = 0;
  var iv = setInterval(function() {
    attachEnter();
    if (++tries > 10) clearInterval(iv);
  }, 500);
})();
</script>
""")


# ==========================================
# [11] RENDER / TAMPILKAN KE LAYAR
# ==========================================
display(btn_cari_style)
display(home_screen)
display(search_screen)
display(enter_js)

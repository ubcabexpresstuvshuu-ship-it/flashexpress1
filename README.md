# flashexpress1
Flash Express – Хүргэлтийн үйлчилгээний вэб
# flash_express_app.py
from flask import Flask, render_template_string, request, redirect, url_for
from jinja2 import DictLoader

app = Flask(__name__)

BASE_HTML = """
<!doctype html>
<html lang="mn">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Флаш Экспресс — Хүргэлтийн цогц үйлчилгээ</title>
  <meta name="description" content="Флаш Экспресс — гараас гарт хурдан шуурхай хүргэлтийн үйлчилгээ. Хүргэлт шалгах, агуулах, оператор, бүсчлэл, апп.">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    :root{ --fx-primary:#FF6A00; --fx-accent:#FFD6A5; --fx-dark:#1f1f29; }
    body{background:#f7f8fc;color:#222}
    .fx-hero{background:linear-gradient(180deg,#ffffff, #fff4ea 70%);}
    .fx-badge{background:var(--fx-primary); color:#fff; border-radius:14px; padding:4px 10px; font-weight:600;}
    .fx-btn{background:var(--fx-primary); color:#fff; border:none;}
    .fx-btn:hover{filter:brightness(0.95); color:#fff;}
    .fx-logo{display:flex; align-items:center; gap:.6rem; text-decoration:none; color:var(--fx-dark); font-weight:800;}
    .fx-logo-icon{width:36px;height:36px;border-radius:10px;background:linear-gradient(135deg,#FF6A00,#FFA84D);display:grid;place-items:center;color:#fff;font-weight:900;box-shadow:0 6px 18px rgba(255,106,0,.12)}
    .fx-card{border:0;border-radius:18px; box-shadow:0 8px 30px rgba(31,31,41,.06);}
    .fx-section{padding:64px 0;}
    .fx-stat{font-size:1.1rem; font-weight:700;}
    .timeline{position:relative; padding-left:28px;}
    .timeline::before{content:""; position:absolute; left:12px; top:0; bottom:0; width:4px; background:#ffe2cc; border-radius:4px;}
    .tl-item{position:relative; margin-bottom:28px;}
    .tl-item::before{content:""; position:absolute; left:-2px; top:4px; width:16px; height:16px; background:var(--fx-primary); border:3px solid #fff; border-radius:50%; box-shadow:0 0 0 4px rgba(255,106,0,0.12);}
    footer{background:var(--fx-primary); color:#fff;}
    a.nav-link{color:#333}
    a.nav-link.active, a.nav-link:hover{color:var(--fx-primary)}
    .form-check-input:checked{background-color:var(--fx-primary); border-color:var(--fx-primary)}
    .rounded-20{border-radius:20px;}
  </style>
</head>
<body>

<nav class="navbar navbar-expand-lg bg-white border-bottom sticky-top">
  <div class="container py-2">
    <a class="fx-logo" href="{{ url_for('home') }}">
      <div class="fx-logo-icon">⚡</div>
      <span>Флаш Экспресс</span>
    </a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#nav">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div id="nav" class="collapse navbar-collapse">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link {{ 'active' if page=='home' else '' }}" href="{{ url_for('home') }}">Нүүр</a></li>
        <li class="nav-item"><a class="nav-link {{ 'active' if page=='services' else '' }}" href="{{ url_for('services') }}">Үйлчилгээ</a></li>
        <li class="nav-item"><a class="nav-link {{ 'active' if page=='zones' else '' }}" href="{{ url_for('zones') }}">Манай бүсчлэл</a></li>
        <li class="nav-item"><a class="nav-link {{ 'active' if page=='timeline' else '' }}" href="{{ url_for('timeline') }}">Ажиллагааны явц</a></li>
        <li class="nav-item"><a class="nav-link {{ 'active' if page=='app_page' else '' }}" href="{{ url_for('app_page') }}">Аппликэйшн</a></li>
        <li class="nav-item"><a class="nav-link {{ 'active' if page=='about' else '' }}" href="{{ url_for('about') }}">Бидний тухай</a></li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">Нэвтрэх</a>
          <ul class="dropdown-menu dropdown-menu-end">
            <li><a class="dropdown-item" href="{{ url_for('login') }}">Нэвтрэх</a></li>
            <li><a class="dropdown-item" href="{{ url_for('register') }}">Бүртгүүлэх</a></li>
          </ul>
        </li>
        <li class="nav-item"><a class="btn fx-btn ms-lg-3" data-bs-toggle="modal" data-bs-target="#trackModal">Хүргэлт шалгах</a></li>
      </ul>
    </div>
  </div>
</nav>

{% block content %}{% endblock %}

<footer class="mt-5">
  <div class="container py-5">
    <div class="row g-4 align-items-start">
      <div class="col-md-5">
        <div class="d-flex align-items-center gap-2 mb-2">
          <div class="fx-logo-icon">⚡</div><h5 class="m-0">Флаш Экспресс</h5>
        </div>
        <p class="mb-1">Монголд гараас гарт хурдан шуурхай хүргэлтийн үйлчилгээ.</p>
        <small>© 2025. Бүх эрх хуулиар хамгаалагдсан.</small>
      </div>
      <div class="col-md-4">
        <h6 class="fw-bold">Хаяг & Холбоо</h6>
        <p class="mb-1">Улаанбаатар хот, Баянгол дүүрэг</p>
        <p class="mb-1">Имэйл: {{ contact_email }}</p>
        <p class="mb-0">Утас: {{ contact_phone }}</p>
      </div>
      <div class="col-md-3">
        <h6 class="fw-bold">Сошиал</h6>
        <div class="d-flex gap-2 flex-wrap">
          <span class="badge bg-light text-dark">Facebook</span>
          <span class="badge bg-light text-dark">YouTube</span>
          <span class="badge bg-light text-dark">WhatsApp</span>
        </div>
      </div>
    </div>
  </div>
</footer>

<!-- Track modal -->
<div class="modal fade" id="trackModal" tabindex="-1">
  <div class="modal-dialog">
    <form class="modal-content" method="post" action="{{ url_for('track') }}">
      <div class="modal-header"><h5 class="modal-title">Хүргэлт шалгах</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal"></button></div>
      <div class="modal-body">
        <label class="form-label">Дугаар/Утас</label>
        <input name="q" class="form-control" placeholder="Ж: 13229... эсвэл 89xx xxxx" required>
        <div class="form-text">Demo өгөгдөл ашиглана.</div>
      </div>
      <div class="modal-footer"><button class="btn fx-btn" type="submit">Шалгах</button></div>
    </form>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
"""

HOME_HTML = """
{% extends 'base.html' %}{% block content %}
<section class="fx-hero fx-section">
  <div class="container">
    <div class="row align-items-center g-4">
      <div class="col-lg-6">
        <span class="fx-badge">Буухиа хүргэлт</span>
        <h1 class="display-5 fw-bold mt-3">Хүргэлтийн <span style="color:var(--fx-primary)">асуудлаа</span> бидэнд даатга</h1>
        <p class="lead">Доорх товчоор хүргэлтээ хаана явж байгааг шууд шалга.</p>
        <div class="d-flex gap-2">
          <a class="btn fx-btn btn-lg" data-bs-toggle="modal" data-bs-target="#trackModal">Хүргэлт шалгах →</a>
          <a class="btn btn-outline-dark btn-lg" href="{{ url_for('services') }}">Үйлчилгээ</a>
        </div>
        <div class="row mt-4 text-center">
          <div class="col-4"><div class="fx-stat">10,000+</div><small>Харилцагч</small></div>
          <div class="col-4"><div class="fx-stat">30+</div><small>Бүсчлэл</small></div>
          <div class="col-4"><div class="fx-stat">1,000+</div><small>Хүргэлт/өдөр</small></div>
        </div>
      </div>
      <div class="col-lg-6">
        <div class="fx-card p-4">
          <h5 class="fw-bold">Харилцагчийн аппликэйшн</h5>
          <p class="mb-3">Хүргэлтээ хянах, захиалга өгөх, тайлан татах боломжтой.</p>
          <img class="img-fluid rounded-20" alt="App mock"
            src="https://dummyimage.com/900x520/fff0e6/ff6a00.png&text=Flash+Express+App"/>
        </div>
      </div>
    </div>
  </div>
</section>
{% endblock %}
"""

SERVICES_HTML = """
{% extends 'base.html' %}{% block content %}
<section class="fx-section">
  <div class="container">
    <h2 class="fw-bold mb-4">Манай үйлчилгээ</h2>
    <div class="row g-4">
      <div class="col-lg-6"><div class="fx-card p-4 h-100">
        <h4 class="fw-bold">1. Хүргэлтийн үйлчилгээ</h4>
        <p>Онлайн бизнесийн барааг авчирч ангилан, бүсчлэлээр гараас гарт хүргэнэ.</p>
        <ul><li>09:00–18:00 хүргэлтийн цонх</li><li>COD/урьдчилсан төлбөр</li><li>Realtime tracking, SMS</li></ul>
      </div></div>
      <div class="col-lg-6"><div class="fx-card p-4 h-100">
        <h4 class="fw-bold">2. Агуулах үйлчилгээ</h4>
        <p>Барааг агуулахад байршуулаад шууд захиалга үүсгэн нийлүүлэлт хийх боломж.</p>
      </div></div>
      <div class="col-lg-6"><div class="fx-card p-4 h-100">
        <h4 class="fw-bold">3. Операторын үйлчилгээ</h4>
        <p>Хаяг тодруулалт, хэрэглэгчтэй холбогдох ажлыг бид гүйцэтгэнэ.</p>
      </div></div>
      <div class="col-lg-6"><div class="fx-card p-4 h-100">
        <h4 class="fw-bold">4. Тайлан & API</h4>
        <p>E-commerce тайлан, CSV/PDF экспорт, API холболт.</p>
      </div></div>
    </div>
  </div>
</section>
{% endblock %}
"""

ZONES_HTML = """
{% extends 'base.html' %}{% block content %}
<section class="fx-section">
  <div class="container">
    <h2 class="fw-bold text-center mb-2">МАНАЙ БҮСЧЛЭЛ</h2>
    <p class="text-center text-muted mb-4">Дээрх бүсээс хэтэрсэн тохиолдолд километр тутамд ₮1,200 нэмэгдэнэ.</p>
    <div class="fx-card p-2">
      <img class="img-fluid rounded-20"
        src="https://dummyimage.com/1100x540/fff0e6/333.png&text=Ulaanbaatar+Delivery+Zones+Map"
        alt="Zones map">
    </div>
  </div>
</section>
{% endblock %}
"""

ABOUT_HTML = """
{% extends 'base.html' %}{% block content %}
<section class="fx-section">
  <div class="container">
    <h2 class="fw-bold mb-3">Бидний тухай</h2>
    <p class="lead">Флаш Экспресс 2019 оноос хойш гараас гарт шуурхай хүргэлтийн үйлчилгээг хөгжүүлж байна.</p>
    <div class="row g-4">
      <div class="col-md-6"><div class="fx-card p-4 h-100">
        <h5 class="fw-bold">Алсын хараа</h5>
        <p>Хэрэглэгч–хэрэглэгчийн гүүр болж инновац нэвтрүүлэн салбартаа тэргүүлэх.</p>
      </div></div>
      <div class="col-md-6"><div class="fx-card p-4 h-100">
        <h5 class="fw-bold">Эрхэм зорилго</h5>
        <p>Стандарт, хугацаа, найдвартай байдлыг мөрдөж үнэ цэнийг хүргэх.</p>
      </div></div>
    </div>
  </div>
</section>
{% endblock %}
"""

TIMELINE_HTML = """
{% extends 'base.html' %}{% block content %}
<section class="fx-section">
  <div class="container">
    <h2 class="fw-bold mb-4">Өдрийн ажиллагааны явц</h2>
    <div class="timeline">
      <div class="tl-item"><h5 class="fw-bold">01 — Захиалга авах</h5>
        <p>21:00 хүртэл маргаашийн хүргэлтийн захиалгыг олон сувгаар авна.</p></div>
      <div class="tl-item"><h5 class="fw-bold">02 — Агуулах</h5>
        <p>23:00–02:00 барааг авчирч ялган ангилна.</p></div>
      <div class="tl-item"><h5 class="fw-bold">03 — Хүргэлт</h5>
        <p>09:00 цагаас эхлэн бүсчлэл бүрд хүргэлт гарна.</p></div>
      <div class="tl-item"><h5 class="fw-bold">04 — Хүргэлт дуусах</h5>
        <p>09:00–18:00 хооронд хүргэлтүүдийн ~95% хүргэгдэнэ (замын ачааллаас хамаарна).</p></div>
      <div class="tl-item"><h5 class="fw-bold">05 — Тайлан</h5>
        <p>Төлөвүүд системд бүртгэгдэж харилцагчид тайлан илгээнэ.</p></div>
    </div>
  </div>
</section>
{% endblock %}
"""

APP_HTML = """
{% extends 'base.html' %}{% block content %}
<section class="fx-section">
  <div class="container">
    <div class="row g-4 align-items-center">
      <div class="col-lg-6">
        <h2 class="fw-bold mb-2">Харилцагчийн аппликэйшн</h2>
        <p>Хүргэлтээ хаана явж байгааг хянах, захиалга үүсгэх, агуулахын тоо бүртгэх, тайлан тооцоо татах боломжтой.</p>
        <div class="d-flex gap-2 flex-wrap">
          <span class="badge bg-light text-dark">GET IT ON Google Play</span>
          <span class="badge bg-light text-dark">GET IT ON App Store</span>
        </div>
      </div>
      <div class="col-lg-6">
        <div class="fx-card p-4">
          <div class="row g-3">
            <div class="col-6 text-center">
              <img class="img-fluid" alt="QR Google Play"
                src="https://api.qrserver.com/v1/create-qr-code/?size=240x240&data=https://play.google.com/"/>
              <div class="small text-muted mt-2">Google Play QR</div>
            </div>
            <div class="col-6 text-center">
              <img class="img-fluid" alt="QR App Store"
                src="https://api.qrserver.com/v1/create-qr-code/?size=240x240&data=https://apple.com/app-store/"/>
              <div class="small text-muted mt-2">App Store QR</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>
{% endblock %}
"""

LOGIN_HTML = """
{% extends 'base.html' %}{% block content %}
<section class="fx-section">
  <div class="container" style="max-width:520px;">
    <div class="fx-card p-4 p-md-5">
      <div class="text-center mb-3">
        <div class="fx-logo-icon mx-auto mb-2">⚡</div>
        <h4 class="m-0">Флаш Экспресс</h4>
      </div>
      {% if message %}<div class="alert alert-warning">{{ message }}</div>{% endif %}
      <form method="post" action="{{ url_for('login') }}">
        <div class="mb-3"><label class="form-label">Нэвтрэх нэр</label><input class="form-control" name="username" required></div>
        <div class="mb-3"><label class="form-label">Нууц үг</label><input type="password" class="form-control" name="password" required></div>
        <div class="form-check mb-3"><input class="form-check-input" type="checkbox" id="remember"><label class="form-check-label" for="remember">Намайг сана</label></div>
        <button class="btn fx-btn w-100 mb-2" type="submit">Нэвтрэх</button>
        <a class="btn btn-outline-dark w-100" href="{{ url_for('register') }}">Бүртгүүлэх</a>
      </form>
      <div class="text-center mt-3"><a href="#" class="small">Нууц үгээ мартсан</a></div>
    </div>
  </div>
</section>
{% endblock %}
"""

REGISTER_HTML = """
{% extends 'base.html' %}{% block content %}
<section class="fx-section">
  <div class="container" style="max-width:560px;">
    <div class="fx-card p-4 p-md-5">
      <h4 class="fw-bold mb-3 text-center">Бүртгүүлэх</h4>
      <form method="post" action="{{ url_for('register') }}">
        <div class="row g-3">
          <div class="col-md-6"><label class="form-label">Нэр</label><input class="form-control" name="first_name" required></div>
          <div class="col-md-6"><label class="form-label">Овог</label><input class="form-control" name="last_name" required></div>
          <div class="col-12"><label class="form-label">Имэйл</label><input type="email" class="form-control" name="email" required></div>
          <div class="col-12"><label class="form-label">Нууц үг</label><input type="password" class="form-control" name="password" required></div>
        </div>
        <button class="btn fx-btn w-100 mt-3" type="submit">Бүртгүүлэх</button>
      </form>
    </div>
  </div>
</section>
{% endblock %}
"""

TRACK_HTML = """
{% extends 'base.html' %}{% block content %}
<section class="fx-section">
  <div class="container">
    <h2 class="fw-bold mb-3">Хайлтын үр дүн</h2>
    <div class="fx-card p-4">
      {% if result %}
        <p class="mb-2"><strong>Хайсан утга:</strong> {{ q }}</p>
        <ul class="mb-0">
          <li><strong>Төлөв:</strong> {{ result['status'] }}</li>
          <li><strong>Сүүлд шинэчлэгдсэн:</strong> {{ result['updated'] }}</li>
          <li><strong>Хаяг:</strong> {{ result['address'] }}</li>
        </ul>
      {% else %}
        <p class="mb-0">Илгээмж олдсонгүй. Туршилтын дугаар: <code>132298473950</code></p>
      {% endif %}
    </div>
    <a class="btn btn-outline-dark mt-3" href="{{ url_for('home') }}">Буцах</a>
  </div>
</section>
{% endblock %}
"""

app.jinja_loader = DictLoader({
    "base.html": BASE_HTML,
    "home.html": HOME_HTML,
    "services.html": SERVICES_HTML,
    "zones.html": ZONES_HTML,
    "about.html": ABOUT_HTML,
    "timeline.html": TIMELINE_HTML,
    "app.html": APP_HTML,
    "login.html": LOGIN_HTML,
    "register.html": REGISTER_HTML,
    "track.html": TRACK_HTML,
})

DEMO = {
    "132298473950": {"status": "Замд / Курьер дээр", "updated": "Өнөөдөр 13:45", "address": "БГД, 8-р хороо, 00-р байр"}
}

@app.context_processor
def inject_contact():
    return {"contact_phone": "94013383", "contact_email": "ubcabexpresstuvshuu@gmail.com"}

@app.route("/")
def home(): return render_template_string(HOME_HTML, page="home")

@app.route("/services")
def services(): return render_template_string(SERVICES_HTML, page="services")

@app.route("/zones")
def zones(): return render_template_string(ZONES_HTML, page="zones")

@app.route("/about")
def about(): return render_template_string(ABOUT_HTML, page="about")

@app.route("/timeline")
def timeline(): return render_template_string(TIMELINE_HTML, page="timeline")

@app.route("/app")
def app_page(): return render_template_string(APP_HTML, page="app_page")

@app.route("/login", methods=["GET","POST"])
def login():
    msg = None
    if request.method == "POST":
        if request.form.get("username") and request.form.get("password"):
            msg = "Demo: Нэвтрэлт амжилттай (жинхэнэ баталгаажуулалт хийгдээгүй)."
        else:
            msg = "Нэвтрэх нэр/нууц үг дутуу."
    return render_template_string(LOGIN_HTML, page="login", message=msg)

@app.route("/register", methods=["GET","POST"])
def register():
    if request.method == "POST":
        return redirect(url_for("login"))
    return render_template_string(REGISTER_HTML, page="register")

@app.post("/track")
def track():
    q = request.form.get("q","").strip()
    result = DEMO.get(q)
    return render_template_string(TRACK_HTML, page="track", q=q, result=result)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=False)

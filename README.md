# instagram-analyzer
<!DOCTYPE html>
<html lang="pt">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Instagram — Quem não me segue de volta?</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
      background: #0a0a0a;
      color: #f0f0f0;
      min-height: 100vh;
    }

    .hero {
      text-align: center;
      padding: 60px 20px 40px;
    }
    .hero h1 {
      font-size: clamp(28px, 5vw, 48px);
      font-weight: 700;
      background: linear-gradient(135deg, #f09433, #e6683c, #dc2743, #cc2366, #bc1888);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
      margin-bottom: 12px;
    }
    .hero p {
      color: #888;
      font-size: 16px;
      max-width: 500px;
      margin: 0 auto;
    }

    .privacy-badge {
      display: inline-flex;
      align-items: center;
      gap: 6px;
      background: #1a1a1a;
      border: 1px solid #2a2a2a;
      border-radius: 99px;
      padding: 6px 14px;
      font-size: 13px;
      color: #888;
      margin-top: 20px;
    }
    .privacy-badge span { color: #4caf50; font-size: 16px; }

    .container {
      max-width: 720px;
      margin: 0 auto;
      padding: 0 20px 60px;
    }

    /* VÍDEO */
    .video-section {
      margin-bottom: 40px;
    }
    .video-section h2 {
      font-size: 18px;
      font-weight: 600;
      margin-bottom: 12px;
      color: #f0f0f0;
    }
    .video-wrapper {
      position: relative;
      padding-bottom: 56.25%;
      border-radius: 16px;
      overflow: hidden;
      background: #111;
      border: 1px solid #222;
    }
    .video-wrapper iframe,
    .video-wrapper video {
      position: absolute;
      top: 0; left: 0;
      width: 100%; height: 100%;
      border: none;
    }

    /* UPLOAD */
    .upload-section {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 16px;
      margin-bottom: 24px;
    }
    @media (max-width: 500px) {
      .upload-section { grid-template-columns: 1fr; }
    }

    .upload-box {
      background: #111;
      border: 1.5px dashed #333;
      border-radius: 14px;
      padding: 28px 16px;
      text-align: center;
      cursor: pointer;
      transition: border-color 0.2s, background 0.2s;
    }
    .upload-box:hover { border-color: #bc1888; background: #1a0f1a; }
    .upload-box.over { border-color: #bc1888; background: #1a0f1a; }
    .upload-box input { display: none; }
    .upload-box .icon { font-size: 36px; margin-bottom: 8px; }
    .upload-box .label { font-size: 13px; color: #888; margin-bottom: 4px; }
    .upload-box code {
      font-size: 12px;
      color: #555;
      background: #1a1a1a;
      padding: 2px 6px;
      border-radius: 4px;
    }

    .status {
      display: flex;
      align-items: center;
      gap: 8px;
      margin-top: 10px;
      padding: 8px 12px;
      border-radius: 8px;
      background: #1a1a1a;
      font-size: 13px;
      color: #888;
      min-height: 38px;
    }
    .status .dot {
      width: 8px; height: 8px; border-radius: 50%; flex-shrink: 0;
      background: #444;
    }
    .dot-ok { background: #4caf50 !important; }
    .dot-err { background: #e53935 !important; }

    /* BOTÃO */
    .btn-analyze {
      width: 100%;
      padding: 14px;
      font-size: 16px;
      font-weight: 600;
      border: none;
      border-radius: 12px;
      background: linear-gradient(135deg, #f09433, #e6683c, #dc2743, #cc2366, #bc1888);
      color: white;
      cursor: pointer;
      transition: opacity 0.2s, transform 0.1s;
      margin-bottom: 32px;
    }
    .btn-analyze:disabled {
      opacity: 0.3;
      cursor: not-allowed;
    }
    .btn-analyze:not(:disabled):hover { opacity: 0.9; transform: translateY(-1px); }
    .btn-analyze:not(:disabled):active { transform: translateY(0); }

    /* RESULTADOS */
    #results { display: none; }

    .stats {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 12px;
      margin-bottom: 24px;
    }
    @media (max-width: 400px) { .stats { grid-template-columns: 1fr 1fr; } }

    .stat-card {
      background: #111;
      border: 1px solid #222;
      border-radius: 12px;
      padding: 16px;
      text-align: center;
    }
    .stat-card .num {
      font-size: 30px;
      font-weight: 700;
      color: #f0f0f0;
    }
    .stat-card .num.red { color: #e53935; }
    .stat-card .lbl { font-size: 12px; color: #666; margin-top: 4px; }

    .results-card {
      background: #111;
      border: 1px solid #222;
      border-radius: 14px;
      padding: 20px;
    }
    .results-card h3 { font-size: 16px; font-weight: 600; margin-bottom: 14px; }

    .search-input {
      width: 100%;
      padding: 10px 14px;
      background: #1a1a1a;
      border: 1px solid #2a2a2a;
      border-radius: 8px;
      color: #f0f0f0;
      font-size: 14px;
      outline: none;
      margin-bottom: 16px;
    }
    .search-input:focus { border-color: #bc1888; }
    .search-input::placeholder { color: #555; }

    .user-list { max-height: 460px; overflow-y: auto; }
    .user-row {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 10px 0;
      border-bottom: 1px solid #1a1a1a;
    }
    .user-row:last-child { border-bottom: none; }
    .user-info { display: flex; align-items: center; gap: 10px; }
    .avatar {
      width: 36px; height: 36px; border-radius: 50%;
      background: #1a1a1a;
      border: 1px solid #2a2a2a;
      display: flex; align-items: center; justify-content: center;
      font-size: 14px; font-weight: 600; color: #888; flex-shrink: 0;
    }
    .ig-link {
      color: #e0e0e0;
      text-decoration: none;
      font-size: 14px;
      font-weight: 500;
    }
    .ig-link:hover { color: #bc1888; }
    .badge {
      font-size: 11px;
      padding: 3px 10px;
      border-radius: 99px;
      background: #2a0a0a;
      color: #e53935;
    }

    .footer-note {
      text-align: center;
      margin-top: 10px;
      font-size: 12px;
      color: #444;
    }
  </style>
</head>
<body>

  <div class="hero">
    <h1>Quem não te segue de volta?</h1>
    <p>Descobre quem não te segue de volta no Instagram. 100% privado — os teus dados nunca saem do teu browser.</p>
    <div class="privacy-badge">
      <span>🔒</span> Processado localmente, sem servidores
    </div>
  </div>

  <div class="container">

    <!-- VÍDEO -->
    <div class="video-section">
      <h2>📹 Como exportar os teus dados do Instagram</h2>
      <div class="video-wrapper">
        <!-- OPÇÃO B: Vídeo próprio (mp4) — descomenta e apaga o iframe acima
        <video controls>
          <source src="tutorial.mp4" type="video/mp4">
        </video>
        -->

      </div>
    </div>

    <!-- UPLOAD -->
    <div class="upload-section">
      <div>
        <div class="upload-box" id="dz-followers" onclick="document.getElementById('inp-followers').click()">
          <input type="file" id="inp-followers" accept=".json">
          <div class="icon">👥</div>
          <div class="label">Os teus seguidores</div>
          <code>followers_1.json</code>
        </div>
        <div class="status" id="status-followers">
          <div class="dot"></div>
          <span>Aguardando ficheiro…</span>
        </div>
      </div>

      <div>
        <div class="upload-box" id="dz-following" onclick="document.getElementById('inp-following').click()">
          <input type="file" id="inp-following" accept=".json">
          <div class="icon">➕</div>
          <div class="label">Quem segues</div>
          <code>following.json</code>
        </div>
        <div class="status" id="status-following">
          <div class="dot"></div>
          <span>Aguardando ficheiro…</span>
        </div>
      </div>
    </div>

    <button class="btn-analyze" id="btn-analyze" onclick="analyze()" disabled>
      🔍 Analisar
    </button>

    <!-- RESULTADOS -->
    <div id="results">
      <div class="stats">
        <div class="stat-card">
          <div class="num" id="stat-following">0</div>
          <div class="lbl">Segues</div>
        </div>
        <div class="stat-card">
          <div class="num" id="stat-followers">0</div>
          <div class="lbl">Seguidores</div>
        </div>
        <div class="stat-card">
          <div class="num red" id="stat-nf">0</div>
          <div class="lbl">Não seguem de volta</div>
        </div>
      </div>

      <div class="results-card">
        <h3>❌ Não seguem de volta</h3>
        <input class="search-input" type="text" id="search"
               placeholder="Pesquisar por username…" oninput="renderList()">
        <div class="user-list" id="list-container"></div>
        <p class="footer-note">Clica no username para abrir o perfil no Instagram</p>
      </div>
    </div>

  </div>

  <script>
    let followersSet = null;
    let followingList = null;
    let notFollowingBack = [];

    function extractUsernames(data) {
      const usernames = new Set();
      const arr = Array.isArray(data)
        ? data
        : (data.relationships_following || data.relationships_followers
           || Object.values(data).find(v => Array.isArray(v)) || []);

      arr.forEach(item => {
        if (item.title && typeof item.title === 'string' && item.title.trim()) {
          usernames.add(item.title.trim().toLowerCase());
          return;
        }
        if (item.string_list_data) {
          item.string_list_data.forEach(e => {
            if (e.value && e.value.trim()) usernames.add(e.value.trim().toLowerCase());
          });
        }
      });
      return usernames;
    }

    function extractFull(data) {
      const entries = [];
      const arr = Array.isArray(data)
        ? data
        : (data.relationships_following || data.relationships_followers
           || Object.values(data).find(v => Array.isArray(v)) || []);

      arr.forEach(item => {
        if (item.title && typeof item.title === 'string' && item.title.trim()) {
          entries.push({ username: item.title.trim() });
          return;
        }
        if (item.string_list_data) {
          item.string_list_data.forEach(e => {
            if (e.value && e.value.trim()) entries.push({ username: e.value.trim() });
          });
        }
      });
      return entries;
    }

    function loadFile(inputId, statusId, key) {
      const inp = document.getElementById(inputId);
      inp.onchange = () => {
        const file = inp.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = (e) => {
          try {
            const parsed = JSON.parse(e.target.result);
            const usernames = extractUsernames(parsed);
            const full = extractFull(parsed);

            if (usernames.size === 0) {
              setStatus(statusId, 'err', '0 entradas — verifica o ficheiro');
              return;
            }

            if (key === 'followers') followersSet = usernames;
            else followingList = full;

            setStatus(statusId, 'ok', `${file.name} — ${usernames.size} entradas`);
            checkReady();
          } catch (err) {
            setStatus(statusId, 'err', 'Erro ao ler ficheiro');
          }
        };
        reader.readAsText(file);
      };
    }

    function setStatus(id, type, text) {
      const el = document.getElementById(id);
      el.querySelector('.dot').className = 'dot' + (type === 'ok' ? ' dot-ok' : type === 'err' ? ' dot-err' : '');
      el.querySelector('span').textContent = text;
    }

    function checkReady() {
      document.getElementById('btn-analyze').disabled = !(followersSet && followingList);
    }

    function analyze() {
      notFollowingBack = [];
      followingList.forEach(entry => {
        if (!followersSet.has(entry.username.toLowerCase())) {
          notFollowingBack.push(entry.username);
        }
      });

      document.getElementById('stat-following').textContent = followingList.length;
      document.getElementById('stat-followers').textContent = followersSet.size;
      document.getElementById('stat-nf').textContent = notFollowingBack.length;
      document.getElementById('results').style.display = 'block';
      document.getElementById('search').value = '';
      renderList();
      document.getElementById('results').scrollIntoView({ behavior: 'smooth' });
    }

    function renderList() {
      const q = document.getElementById('search').value.toLowerCase().trim();
      const list = q ? notFollowingBack.filter(u => u.toLowerCase().includes(q)) : notFollowingBack;
      const container = document.getElementById('list-container');

      if (list.length === 0) {
        container.innerHTML = '<p style="text-align:center; color:#555; padding: 20px 0;">Nenhum resultado.</p>';
        return;
      }

      container.innerHTML = list.map(u => `
        <div class="user-row">
          <div class="user-info">
            <div class="avatar">${u[0].toUpperCase()}</div>
            <a class="ig-link" href="https://instagram.com/${u}" target="_blank" rel="noopener">@${u}</a>
          </div>
          <span class="badge">não segue</span>
        </div>
      `).join('');
    }

    loadFile('inp-followers', 'status-followers', 'followers');
    loadFile('inp-following', 'status-following', 'following');

    ['dz-followers', 'dz-following'].forEach(id => {
      const dz = document.getElementById(id);
      dz.addEventListener('dragover', e => { e.preventDefault(); dz.classList.add('over'); });
      dz.addEventListener('dragleave', () => dz.classList.remove('over'));
      dz.addEventListener('drop', e => {
        e.preventDefault(); dz.classList.remove('over');
        const inp = document.getElementById(id === 'dz-followers' ? 'inp-followers' : 'inp-following');
        const dt = new DataTransfer();
        dt.items.add(e.dataTransfer.files[0]);
        inp.files = dt.files;
        inp.dispatchEvent(new Event('change'));
      });
    });
  </script>
</body>
</html>

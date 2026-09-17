<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mobile Code Runner</title>
  <style>
    :root {
      --bg: #1e1e2e;
      --panel-bg: #2b2b3d;
      --text: #cdd6f4;
      --accent: #89b4fa;
      --border: #45475a;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: -apple-system, BlinkMacSystemFont, sans-serif;
    }

    body {
      background-color: var(--bg);
      color: var(--text);
      display: flex;
      flex-direction: column;
      height: 100vh;
      overflow: hidden;
    }

    header {
      background-color: var(--panel-bg);
      padding: 10px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 1px solid var(--border);
    }

    h2 {
      font-size: 1.1rem;
      color: var(--accent);
    }

    .toolbar {
      display: flex;
      gap: 8px;
    }

    button, label.btn {
      background: var(--accent);
      color: var(--bg);
      border: none;
      padding: 8px 12px;
      font-weight: bold;
      border-radius: 6px;
      cursor: pointer;
      font-size: 0.85rem;
      touch-action: manipulation;
    }

    input[type="file"] {
      display: none;
    }

    /* タブ切り替えボタン (スマホ用) */
    .tab-bar {
      display: flex;
      background: #181825;
      border-bottom: 1px solid var(--border);
    }

    .tab-btn {
      flex: 1;
      padding: 10px;
      background: transparent;
      color: #a6adc8;
      border: none;
      border-bottom: 2px solid transparent;
      border-radius: 0;
      font-size: 0.9rem;
    }

    .tab-btn.active {
      color: var(--accent);
      border-bottom-color: var(--accent);
      background: var(--panel-bg);
    }

    .main-container {
      flex: 1;
      position: relative;
      height: calc(100vh - 100px);
    }

    .pane {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      display: none;
      flex-direction: column;
    }

    .pane.active {
      display: flex;
    }

    textarea {
      width: 100%;
      height: 100%;
      background: var(--bg);
      color: var(--text);
      border: none;
      padding: 12px;
      font-family: monospace;
      font-size: 0.95rem;
      resize: none;
      outline: none;
      line-height: 1.4;
    }

    iframe {
      width: 100%;
      height: 100%;
      border: none;
      background: #ffffff;
    }
  </style>
</head>
<body>

  <header>
    <h2>Code Runner</h2>
    <div class="toolbar">
      <label class="btn" for="fileInput">ファイル選択</label>
      <input type="file" id="fileInput" accept=".html,.txt">
      <button id="runBtn">実行</button>
    </div>
  </header>

  <!-- スマホ向け画面切り替えタブ -->
  <div class="tab-bar">
    <button class="tab-btn active" id="tabEditor" onclick="switchTab('editor')">コード編集</button>
    <button class="tab-btn" id="tabPreview" onclick="switchTab('preview')">実行画面</button>
  </div>

  <div class="main-container">
    <!-- エディタ画面 -->
    <div class="pane active" id="editorPane">
      <textarea id="codeEditor" placeholder="ここにHTML/CSS/JSを入力..."></textarea>
    </div>

    <!-- プレビュー画面 -->
    <div class="pane" id="previewPane">
      <iframe id="previewFrame"></iframe>
    </div>
  </div>

  <script>
    const codeEditor = document.getElementById('codeEditor');
    const previewFrame = document.getElementById('previewFrame');
    const runBtn = document.getElementById('runBtn');
    const fileInput = document.getElementById('fileInput');

    // タブ切り替え処理
    function switchTab(tabName) {
      const editorPane = document.getElementById('editorPane');
      const previewPane = document.getElementById('previewPane');
      const tabEditor = document.getElementById('tabEditor');
      const tabPreview = document.getElementById('tabPreview');

      if (tabName === 'editor') {
        editorPane.classList.add('active');
        previewPane.classList.remove('active');
        tabEditor.classList.add('active');
        tabPreview.classList.remove('active');
      } else {
        editorPane.classList.remove('active');
        previewPane.classList.add('active');
        tabEditor.classList.remove('active');
        tabPreview.classList.add('active');
      }
    }

    // コード実行
    function runCode() {
      const code = codeEditor.value;
      const previewDocument = previewFrame.contentDocument || previewFrame.contentWindow.document;
      
      previewDocument.open();
      previewDocument.write(code);
      previewDocument.close();
      
      // 実行したら自動的に実行画面タブに切り替え
      switchTab('preview');
    }

    runBtn.addEventListener('click', runCode);

    // スマホのローカルファイル読み込み
    fileInput.addEventListener('change', (e) => {
      const file = e.target.files[0];
      if (!file) return;

      const reader = new FileReader();
      reader.onload = (event) => {
        codeEditor.value = event.target.result;
        runCode();
      };
      reader.readAsText(file);
    });

    // 初期サンプルコード
    codeEditor.value = `<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body { font-family: sans-serif; text-align: center; padding: 30px 15px; }
    button { padding: 12px 24px; font-size: 1rem; background: #89b4fa; border: none; border-radius: 8px; color: #1e1e2e; font-weight: bold; }
  </style>
</head>
<body>
  <h3>スマホ動作テスト</h3>
  <p>コードを入力・読み込みして「実行」を押してください。</p>
  <br>
  <button onclick="alert('スマホ上で正常に動作しました！')">タップしてテスト</button>
</body>
</html>`;
  </script>
</body>
</html>

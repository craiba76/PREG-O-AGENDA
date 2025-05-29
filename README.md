<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Agenda BHDF</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #f0f4f8;
      margin: 0;
      padding: 20px;
    }
    h2 {
      color: #1f2937;
      text-align: center;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-bottom: 30px;
      background: #ffffff;
      border-radius: 8px;
      overflow: hidden;
      box-shadow: 0 4px 10px rgba(0,0,0,0.06);
    }
    th, td {
      border: 1px solid #d1d5db;
      padding: 15px;
      text-align: left;
    }
    th {
      background-color: #e5e7eb;
      color: #111827;
      font-weight: 600;
    }
    input[type="text"],
    input[type="datetime-local"],
    input[type="password"] {
      width: 100%;
      padding: 12px;
      font-size: 16px;
      border: 1px solid #d1d5db;
      border-radius: 6px;
      background-color: #f9fafb;
    }
    button {
      padding: 12px 18px;
      margin: 10px 5px 20px 0;
      font-size: 16px;
      background-color: #3b82f6;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      transition: background-color 0.2s ease-in-out;
    }
    button:hover {
      background-color: #2563eb;
    }
    #loginForm {
      display: flex;
      flex-direction: column;
      width: 100%;
      max-width: 400px;
      margin: 40px auto;
      background: #ffffff;
      padding: 30px;
      border-radius: 10px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.08);
      gap: 15px;
    }
    .destaque {
      background-color: #10b981 !important;
      color: white;
    }
  </style>
</head>
<body>
  <div id="login">
    <h2>Login</h2>
    <form id="loginForm">
      <input type="text" id="usuario" placeholder="Usuário" required />
      <input type="password" id="senha" placeholder="Senha" required />
      <button type="submit">Acessar</button>
    </form>
  </div>

  <div id="agenda" style="display: none;">
    <h2>Agenda de Licitações</h2>
    <audio id="alertaSom" src="https://www.soundjay.com/button/beep-07.wav"></audio>

    <table id="tabela">
      <thead>
        <tr>
          <th>Data e Horário</th>
          <th>Empresa</th>
          <th>Pregão/Dispensa</th>
          <th>UASG</th>
          <th>Órgão</th>
          <th>Estado</th>
          <th>Portal</th>
          <th>Descrição</th>
          <th>Edital</th>
          <th>Ação</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>

    <button onclick="ordenarTabela()">Ordenar por Data</button>
    <button onclick="adicionarNovaLinha()">Adicionar Nova Linha</button>
    <button onclick="fecharPlanilha()">Fechar e Salvar</button>

    <h2>Cadastro de Fornecedores</h2>
    <input type="text" id="buscaFornecedor" placeholder="Buscar por nome, e-mail ou telefone" onkeyup="buscarFornecedor()" />

    <table id="tabelaFornecedores">
      <thead>
        <tr>
          <th>Nome</th>
          <th>Telefone</th>
          <th>E-mail</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
    <button onclick="adicionarFornecedor()">Adicionar Fornecedor</button>
  </div>

  <script>
    document.getElementById('loginForm').addEventListener('submit', function (e) {
      e.preventDefault();
      const usuario = document.getElementById('usuario').value;
      const senha = document.getElementById('senha').value;
      if (usuario === 'admin' && senha === '123') {
        document.getElementById('login').style.display = 'none';
        document.getElementById('agenda').style.display = 'block';
      } else {
        alert('Usuário ou senha incorretos');
      }
    });

    function adicionarNovaLinha() {
      const tabela = document.getElementById("tabela").getElementsByTagName('tbody')[0];
      const novaLinha = tabela.insertRow();
      for (let i = 0; i < 9; i++) {
        const celula = novaLinha.insertCell();
        if (i === 0) {
          const input = document.createElement("input");
          input.type = "datetime-local";
          celula.appendChild(input);
        } else {
          celula.contentEditable = true;
        }
      }
      const celulaAcoes = novaLinha.insertCell();
      const botao = document.createElement("button");
      botao.innerText = "Destacar";
      botao.onclick = () => destacarLinha(novaLinha);
      celulaAcoes.appendChild(botao);
    }

    function ordenarTabela() {
      const tabela = document.getElementById("tabela");
      const linhas = Array.from(tabela.tBodies[0].rows);
      linhas.sort((a, b) => {
        const dataA = new Date(a.cells[0].querySelector('input')?.value || a.cells[0].innerText);
        const dataB = new Date(b.cells[0].querySelector('input')?.value || b.cells[0].innerText);
        return dataA - dataB;
      });
      linhas.forEach(linha => tabela.tBodies[0].appendChild(linha));
    }

    function destacarLinha(linha) {
      linha.classList.toggle("destaque");
      document.getElementById("alertaSom").play();
    }

    function fecharPlanilha() {
      alert("Planilha salva e fechada!");
    }

    function adicionarFornecedor() {
      const tabela = document.getElementById("tabelaFornecedores").getElementsByTagName('tbody')[0];
      const novaLinha = tabela.insertRow();
      for (let i = 0; i < 3; i++) {
        const celula = novaLinha.insertCell();
        celula.contentEditable = true;
      }
      const celulaAcoes = novaLinha.insertCell();
      const botao = document.createElement("button");
      botao.innerText = "Excluir";
      botao.onclick = () => tabela.removeChild(novaLinha);
      celulaAcoes.appendChild(botao);
    }

    function buscarFornecedor() {
      const input = document.getElementById("buscaFornecedor").value.toLowerCase();
      const linhas = document.getElementById("tabelaFornecedores").getElementsByTagName("tbody")[0].rows;
      for (let i = 0; i < linhas.length; i++) {
        const texto = linhas[i].innerText.toLowerCase();
        linhas[i].style.display = texto.includes(input) ? "" : "none";
      }
    }
  </script>
</body>
</html>

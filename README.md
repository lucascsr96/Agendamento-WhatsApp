<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Agendamento WhatsApp</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0; padding: 0;
      height: 100vh;
      background: linear-gradient(135deg, #7b2ff7, #f107a3);
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .container {
      background: white;
      padding: 30px 25px;
      border-radius: 12px;
      box-shadow: 0 8px 20px rgba(0,0,0,0.2);
      max-width: 400px;
      width: 90%;
      box-sizing: border-box;
    }

    h2 {
      color: #6200ee;
      text-align: center;
      margin-bottom: 20px;
    }

    form input, form select, form button {
      width: 100%;
      padding: 12px;
      margin-top: 12px;
      font-size: 16px;
      border: 1px solid #ccc;
      border-radius: 8px;
      box-sizing: border-box;
      transition: border-color 0.3s ease;
    }

    form input:focus, form select:focus {
      border-color: #6200ee;
      outline: none;
    }

    form button {
      background: #6200ee;
      color: white;
      border: none;
      cursor: pointer;
      margin-top: 20px;
      font-weight: bold;
      border-radius: 8px;
      transition: background-color 0.3s ease;
    }

    form button:hover {
      background: #4b00c4;
    }

    #mensagem {
      margin-top: 18px;
      text-align: center;
      font-weight: 600;
      color: #2e7d32;
      font-size: 18px;
      display: none;
    }
  </style>
</head>
<body>

  <div class="container">
    <h2>Agendamento WhatsApp</h2>
    <form id="formAgendamento">
      <input type="text" id="nome" placeholder="Seu nome" required />
      <input type="tel" id="telefone" placeholder="Seu WhatsApp (somente números)" required />
      <select id="servico" required>
        <option value="">Selecione o serviço</option>
        <option>Barbearia</option>
        <option>Cabeleireiro</option>
        <option>Manicure</option>
        <option>Nutricionista</option>
      </select>
      <input type="date" id="data" required />
      <input type="time" id="hora" min="09:00" max="18:00" step="1800" required />
      <button type="submit">Agendar pelo WhatsApp</button>
    </form>

    <div id="mensagem"></div>
  </div>

  <script>
    const form = document.getElementById('formAgendamento');
    const mensagem = document.getElementById('mensagem');
    const dataInput = document.getElementById('data');

    // Número do profissional com DDI e DDD (ex: 55 11 999999999)
    const telefoneProfissional = "5524936180096"; // SUBSTITUA AQUI

    // Função para bloquear domingos no campo data
    dataInput.addEventListener('input', function() {
      const dataSelecionada = new Date(this.value + 'T00:00:00'); // evita timezone
      if (dataSelecionada.getDay() === 0) { // domingo = 0
        alert("Domingos não estão disponíveis para agendamento. Por favor, escolha outro dia.");
        this.value = "";
      }
    });

    form.addEventListener('submit', function(e) {
      e.preventDefault();

      const nome = document.getElementById('nome').value.trim();
      const telefoneCliente = document.getElementById('telefone').value.replace(/\D/g, '');
      const servico = document.getElementById('servico').value;
      const data = document.getElementById('data').value;
      const hora = document.getElementById('hora').value;

      if (!nome || !telefoneCliente || !servico || !data || !hora) {
        alert('Por favor, preencha todos os campos corretamente.');
        return;
      }

      // Verifica novamente se a data não é domingo (proteção extra)
      const diaSemana = new Date(data + 'T00:00:00').getDay();
      if (diaSemana === 0) {
        alert("Domingos não são permitidos para agendamento.");
        return;
      }

      // Mensagem para o cliente
      const msgCliente = 
`Olá, ${nome}! Seu agendamento foi confirmado:
- Serviço: ${servico}
- Data: ${data}
- Hora: ${hora}
Obrigado!`;

      // Mensagem para o profissional
      const msgProfissional =
`Novo agendamento:
Cliente: ${nome}
Telefone: +55${telefoneCliente}
Serviço: ${servico}
Data: ${data}
Hora: ${hora}`;

      // URLs WhatsApp
      const urlCliente = `https://wa.me/55${telefoneCliente}?text=${encodeURIComponent(msgCliente)}`;
      const urlProfissional = `https://wa.me/${telefoneProfissional}?text=${encodeURIComponent(msgProfissional)}`;

      // Abre abas WhatsApp
      window.open(urlCliente, '_blank');
      window.open(urlProfissional, '_blank');

      form.reset();

      // Mostrar mensagem de confirmação
      mensagem.style.display = 'block';
      mensagem.textContent = "✅ Agendamento enviado! Confira seu WhatsApp.";

      // Esconder a mensagem depois de 5 segundos
      setTimeout(() => {
        mensagem.style.display = 'none';
      }, 5000);
    });
  </script>

</body>
</html>

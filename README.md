function confirmCancelRITM() {
  // evita criar mais de um modal se o usuário clicar várias vezes
  if (document.getElementById('confirmCancelRITMModal')) return;

  // cria overlay
  var overlay = document.createElement('div');
  overlay.id = 'confirmCancelRITMOverlay';
  overlay.style.position = 'fixed';
  overlay.style.top = 0;
  overlay.style.left = 0;
  overlay.style.width = '100%';
  overlay.style.height = '100%';
  overlay.style.background = 'rgba(0,0,0,0.45)';
  overlay.style.zIndex = 9999;
  overlay.style.display = 'flex';
  overlay.style.alignItems = 'center';
  overlay.style.justifyContent = 'center';

  // cria modal
  var modal = document.createElement('div');
  modal.id = 'confirmCancelRITMModal';
  modal.style.width = '420px';
  modal.style.maxWidth = '90%';
  modal.style.background = '#fff';
  modal.style.borderRadius = '8px';
  modal.style.boxShadow = '0 6px 24px rgba(0,0,0,0.3)';
  modal.style.padding = '20px';
  modal.style.fontFamily = 'Arial, sans-serif';
  modal.style.color = '#222';

  // conteúdo
  var title = document.createElement('h3');
  title.innerText = 'Confirmação de Cancelamento';
  title.style.margin = '0 0 10px 0';
  title.style.fontSize = '18px';

  var message = document.createElement('p');
  message.innerText = 'Tem certeza que deseja cancelar esta RITM?';
  message.style.margin = '0 0 20px 0';
  message.style.fontSize = '14px';

  // botões
  var btnContainer = document.createElement('div');
  btnContainer.style.display = 'flex';
  btnContainer.style.justifyContent = 'flex-end';
  btnContainer.style.gap = '8px';

  var cancelBtn = document.createElement('button');
  cancelBtn.type = 'button';
  cancelBtn.innerText = '❌ Cancelar';
  cancelBtn.style.padding = '8px 12px';
  cancelBtn.style.border = '1px solid #bbb';
  cancelBtn.style.borderRadius = '6px';
  cancelBtn.style.background = '#fff';
  cancelBtn.style.cursor = 'pointer';

  var confirmBtn = document.createElement('button');
  confirmBtn.type = 'button';
  confirmBtn.innerText = '✅ Confirmar';
  confirmBtn.style.padding = '8px 12px';
  confirmBtn.style.border = 'none';
  confirmBtn.style.borderRadius = '6px';
  confirmBtn.style.background = '#d9534f'; // tom de "destructive"
  confirmBtn.style.color = '#fff';
  confirmBtn.style.cursor = 'pointer';

  // acessibilidade: foco no botão confirmar
  confirmBtn.autofocus = true;

  btnContainer.appendChild(cancelBtn);
  btnContainer.appendChild(confirmBtn);

  modal.appendChild(title);
  modal.appendChild(message);
  modal.appendChild(btnContainer);
  overlay.appendChild(modal);
  document.body.appendChild(overlay);

  // função para remover modal
  function removeModal() {
    var o = document.getElementById('confirmCancelRITMOverlay');
    if (o) o.parentNode.removeChild(o);
  }

  // handlers
  cancelBtn.addEventListener('click', function () {
    removeModal();
    if (typeof gsftMain !== 'undefined') {
      gsftMain.clearMessages();
      gsftMain.addInfoMessage('Ação de cancelamento cancelada.');
    }
  });

  confirmBtn.addEventListener('click', function () {
    // desabilita botões enquanto processa
    confirmBtn.disabled = true;
    cancelBtn.disabled = true;
    confirmBtn.innerText = 'Processando...';

    // chama o Script Include via GlideAjax
    try {
      var ga = new GlideAjax('CancelRITMUtils');
      ga.addParam('sysparm_name', 'cancelRITM');
      ga.addParam('sysparm_sys_id', g_form.getUniqueValue());
      ga.getXMLAnswer(function (response) {
        removeModal();
        if (response && response.toLowerCase().indexOf('sucesso') !== -1) {
          if (typeof gsftMain !== 'undefined') {
            gsftMain.clearMessages();
            gsftMain.addInfoMessage('✅ RITM cancelada com sucesso!');
          } else {
            alert('RITM cancelada com sucesso!');
          }
        } else {
          if (typeof gsftMain !== 'undefined') {
            gsftMain.clearMessages();
            gsftMain.addErrorMessage('❌ Erro ao cancelar a RITM: ' + response);
          } else {
            alert('Erro ao cancelar a RITM: ' + response);
          }
        }
        // recarrega para refletir alterações
        try { g_form.reload(); } catch (e) { /* ignore */ }
      });
    } catch (err) {
      removeModal();
      alert('Erro ao iniciar cancelamento: ' + err.message);
    }
  });

  // fecha com ESC
  function escHandler(evt) {
    evt = evt || window.event;
    if (evt.key === 'Escape') {
      removeModal();
      document.removeEventListener('keydown', escHandler);
    }
  }
  document.addEventListener('keydown', escHandler);
}


function onLoad() {
    // Função para abrir o modal - será chamada pela UI Action
    window.openCancelModal = function() {
        var ritmNumber = g_form.getDisplayValue('number');
        var ritmShortDescription = g_form.getDisplayValue('short_description');
        
        var modal = new GlideModal('cancel_ritm_modal', true);
        modal.setTitle('Confirmar Cancelamento do RITM');
        modal.setWidth(500);
        
        var content = 
            '<div style="padding: 20px;">' +
                '<div class="alert alert-warning">' +
                    '<strong>Atenção!</strong> Esta ação não pode ser desfeita.' +
                '</div>' +
                '<div style="margin: 15px 0;">' +
                    '<p><strong>RITM:</strong> ' + ritmNumber + '</p>' +
                    '<p><strong>Item:</strong> ' + ritmShortDescription + '</p>' +
                '</div>' +
                '<div style="background-color: #f8f9fa; padding: 15px; border-radius: 4px; margin: 15px 0;">' +
                    '<p style="margin: 0; color: #721c24;"><strong>Confirma o cancelamento deste item de requisição?</strong></p>' +
                '</div>' +
                '<div style="text-align: right; margin-top: 20px; padding-top: 15px; border-top: 1px solid #ddd;">' +
                    '<button class="btn btn-default" onclick="GlideModal.get().destroy();" style="margin-right: 10px;">' +
                        '<span class="icon icon-undo"></span> Voltar' +
                    '</button>' +
                    '<button class="btn btn-danger" onclick="confirmRITMCancel();">' +
                        '<span class="icon icon-check"></span> Confirmar Cancelamento' +
                    '</button>' +
                '</div>' +
            '</div>';
        
        modal.renderWithContent(content);
    };
    
    // Função para confirmar o cancelamento
    window.confirmRITMCancel = function() {
        try {
            // Fechar o modal
            GlideModal.get().destroy();
            
            // Mostrar mensagem de processamento
            g_form.addInfoMessage('Processando cancelamento...');
            
            // Alterar o state para 7 (Cancelado)
            g_form.setValue('state', '7');
            
            // Adicionar comentário padrão
            var currentComments = g_form.getValue('comments');
            var cancelComment = '\n\n[RITM Cancelado via Interface - ' + new GlideDateTime().getDisplayValue() + ']';
            
            if (currentComments) {
                g_form.setValue('comments', currentComments + cancelComment);
            } else {
                g_form.setValue('comments', 'RITM cancelado pelo usuário.' + cancelComment);
            }
            
            // Salvar o formulário
            g_form.save().then(function(response) {
                if (response && response.isSuccessful()) {
                    g_form.addInfoMessage('RITM cancelado com sucesso!');
                } else {
                    g_form.addErrorMessage('Erro ao cancelar RITM. Tente novamente.');
                }
            });
            
        } catch (error) {
            g_form.addErrorMessage('Erro ao processar cancelamento: ' + error.message);
        }
    };
}

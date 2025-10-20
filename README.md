
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

------

function onClick() {
    // IMPORTANTE: Prevenir o comportamento padrão do botão
    event.preventDefault();
    event.stopPropagation();
    
    // Abrir modal
    var ritmNumber = g_form.getDisplayValue('number');
    var ritmShortDescription = g_form.getDisplayValue('short_description');
    
    var modal = new GlideModal('cancel_ritm_modal');
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
                '<p style="margin: 0; color: #721c24;"><strong>Confirma o cancelamento?</strong></p>' +
            '</div>' +
            '<div style="text-align: right; margin-top: 20px; padding-top: 15px; border-top: 1px solid #ddd;">' +
                '<button class="btn btn-default" onclick="GlideModal.get().destroy();" style="margin-right: 10px;">' +
                    'Voltar' +
                '</button>' +
                '<button class="btn btn-danger" onclick="confirmRITMCancel();">' +
                    'Confirmar Cancelamento' +
                '</button>' +
            '</div>' +
        '</div>';
    
    modal.renderWithContent(content);
    
    // Função global para confirmar
    window.confirmRITMCancel = function() {
        try {
            // Fechar modal
            GlideModal.get().destroy();
            
            // Mostrar mensagem
            g_form.addInfoMessage('Cancelando RITM...');
            
            // Mudar state para 7 (Cancelado)
            g_form.setValue('state', '7');
            
            // Adicionar comentário
            var comments = g_form.getValue('comments') || '';
            g_form.setValue('comments', comments + ' [Cancelado em ' + new GlideDateTime().getDisplayValue() + ']');
            
            // Salvar
            g_form.save();
            
        } catch (error) {
            g_form.addErrorMessage('Erro ao cancelar: ' + error);
        }
    };
    
    return false; // Importante: prevenir submit do formulário
}
------- dai -----
function onClick() {
    // Para UI Actions com show update, precisamos de abordagem diferente
    if (typeof g_form != 'undefined') {
        // Abrir modal diretamente
        openCancelModalNow();
    }
    
    // Sempre retornar false para prevenir reload
    return false;
}

function openCancelModalNow() {
    var ritmNumber = g_form.getDisplayValue('number');
    var ritmShortDescription = g_form.getDisplayValue('short_description');
    
    var modal = new GlideModal('cancel_ritm_modal');
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
            '<div style="text-align: center; margin: 20px 0;">' +
                '<button class="btn btn-default" onclick="GlideModal.get().destroy();" style="margin-right: 10px; padding: 8px 20px;">' +
                    'Voltar' +
                '</button>' +
                '<button class="btn btn-danger" onclick="confirmCancelNow();" style="padding: 8px 20px;">' +
                    'Confirmar Cancelamento' +
                '</button>' +
            '</div>' +
        '</div>';
    
    modal.renderWithContent(content);
    
    window.confirmCancelNow = function() {
        // Fechar modal
        GlideModal.get().destroy();
        
        // Mudar estado e salvar
        g_form.setValue('state', '7');
        
        // Forçar save imediato
        g_form.save().then(function(response) {
            if (response && response.isSuccessful()) {
                g_form.addInfoMessage('RITM cancelado com sucesso!');
                // Recarregar para atualizar interface
                setTimeout(function() {
                    location.reload();
                }, 1500);
            }
        });
    };
}

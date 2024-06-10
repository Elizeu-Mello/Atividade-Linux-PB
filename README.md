# Atividade de Linux PB

1. **Configuração do NFS Server**
    - Conecte-se a instancia EC2 via SSH ou usando o proprio console da Amazon.
    - No Terminal execute o seguinte comando: `sudo yum update -y` Para atualizar o sistema para suas versões mais recentes. 
    - Instale o pacote nfs-utils executando o seguinte comando: `sudo yum install -y nfs-utils` Que contém os utilitários necessários para configurar o servidor NFS.
    
2. **Criação do diretório com seu_nome:**
    - Execute os seguintes comandos:
       - `sudo mkdir -p /srv/nfs/seu_nome` Cria o diretório com o seu nome.
       - `sudo chown -R nobody:nobody /srv/nfs/seu_nome` Muda o proprietário do diretório e de todos os seus arquivos para nobody.
       - `sudo chmod 777 /srv/nfs/seu_nome` Concede permissões de leitura, gravação e execução para todos os usuários no diretório.
         
4. **Editando o arquivo de exportação NFS:**
    - Abre o editor de texto nano para editar o arquivo exports executando o seguinte comando: 
    - `sudo nano /etc/exports` 
       - Adicionar nova linha
            ```
            /srv/nfs/seu_nome *(rw,sync,no_subtree_check)
          ```
         - Esta linha configura o diretório /srv/nfs/seu_nome para ser exportado via NFS, permitindo acesso de leitura e escrita (rw) a qualquer host (*), garantindo que as operações de escrita sejam feitas de forma síncrona (sync) e desativando a verificação de subárvores (no_subtree_check).

5. **Exportando o diretório NFS:**
    - Execute o seguinte comando:
       ```
       sudo exportfs -r
       ```
         - Para Recarrega as exportações NFS, aplicando mudanças feitas no arquivo /etc/exports sem precisar reiniciar o serviço.
  
6. **Inicializando e Habilitando do Serviço NFS**
    - Execute os seguintes comandos:
       - `sudo systemctl start nfs-server` Inicia o serviço do servidor NFS.
       - `sudo systemctl enable nfs-server` Configura o serviço NFS para iniciar automaticamente no boot do sistema.
       - `sudo systemctl status nfs-server` mostra o status do servidor NFS, como ativo, inativo, ou desabilitado.

7. **Instalação e Configuração do Apache**
    - Execute o seguinte comando:
       - `sudo yum install -y httpd` Instala o servidor web Apache.

8. **Iniciar e habilitar o Apache:**
    - Execute os seguintes comandos:
       - `sudo systemctl start httpd` Inicia o serviço do Apache.
       - `sudo systemctl enable httpd` Configura o Apache para iniciar automaticamente no boot do sistema.
       - `systemctl status httpd`  Exibe o status atual do serviço Apache, mostrando se está ativo e funcionando.

9. **Criação e Configuração do Script de Verificação:**
    - Execute o seguinte comandos:
       - `sudo nano /usr/local/bin/status.apache.sh` Para criar e editar o script de verificação do Apache.
         - Adicionar o seguinte conteúdo ao script:
         ```
         #!/bin/bash

         # Diretório de saída
         OUTPUT_DIR="/srv/nfs/seu_nome"

         # Nome do serviço
         SERVICE_NAME="httpd"

         # Data e Hora atuais
         CURRENT_DATE=$(date '+%Y-%m-%d %H:%M:%S')

         # Verificar status do serviço
         if systemctl is-active --quiet $SERVICE_NAME; then
          STATUS="ONLINE"
          MESSAGE="$CURRENT_DATE - $SERVICE_NAME - Status: ONLINE - O serviço está em execução."
          echo "$MESSAGE" > "$OUTPUT_DIR/servico_online.txt"
         else
             STATUS="OFFLINE"
             MESSAGE="$CURRENT_DATE - $SERVICE_NAME - Status: OFFLINE - O serviço não está em execução."
             echo "$MESSAGE" > "$OUTPUT_DIR/servico_offline.txt"
         fi
         ```
10. **Tornar o Script Executável**
    - Execute o seguinte comando:
    - `sudo chmod +x /usr/local/bin/verificacao_apache.sh` Concede permissões de execução ao script.

12. **Configuração do Cron**
    - Execute o seguinte comando:
    - `sudo crontab -e` Abre o editor do crontab para o usuário root.

      - Adicionar a seguinte linha ao cron:
          ```
          */5 * * * * /usr/local/bin/verificacao_apache.sh
          ``` 
       - Esta linha configura o script para ser executado a cada 5 minutos.

**Conclusão**
  - Foram realizadas as seguinte estapas: Instalação e configuração do NFS Server e do Apache, bem como a criação de um script de verificação que monitorará o status do Apache e registrará essa informação a cada 5 minutos em seu diretório.

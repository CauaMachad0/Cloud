# Cloud

# GUIA: CRIANDO VM UBUNTU NA AZURE COM NGINX

===============================================================================
RESUMO EXECUTIVO
===============================================================================
Este guia detalha como criar uma máquina virtual Ubuntu na Azure, configurar 
acesso SSH e instalar o servidor web NGINX seguindo as especificações do 
checkpoint.

===============================================================================
PRÉ-REQUISITOS
===============================================================================
- Conta ativa no Microsoft Azure
- Azure CLI instalado ou acesso ao Portal Azure
- Cliente SSH (PuTTY no Windows ou terminal no Linux/Mac)

===============================================================================
PASSO 1: CRIAÇÃO DO RESOURCE GROUP
===============================================================================

VIA PORTAL AZURE:
1. Acesse portal.azure.com
2. Navegue para "Resource groups" → "Create"
3. Configure:
   - Resource group name: rg-checkpoint1-2tds
   - Region: East US
4. Clique em "Review + create" → "Create"

VIA AZURE CLI:
az group create --name rg-checkpoint1-2tds --location eastus

===============================================================================
PASSO 2: CRIAÇÃO DA MÁQUINA VIRTUAL
===============================================================================

VIA PORTAL AZURE:
1. Vá para "Virtual machines" → "Create" → "Azure virtual machine"
2. BASICS TAB:
   - Resource group: rg-checkpoint1-2tds
   - Virtual machine name: vm-checkpoint1-2tds-rmxxxx (substitua xxxx pelo seu RM)
   - Region: East US
   - Image: Ubuntu Server 20.04 LTS ou Ubuntu Server 22.04 LTS
   - Size: Standard_B1s
   - Authentication type: Password
   - Username: tdsdevops
   - Password: 2tdsdevops@2024

3. NETWORKING TAB:
   - Public inbound ports: Select inbound ports
   - Select inbound ports: SSH (22), HTTP (80)

4. Clique em "Review + create" → "Create"

VIA AZURE CLI:
az vm create \
  --resource-group rg-checkpoint1-2tds \
  --name vm-checkpoint1-2tds-rmxxxx \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --admin-username tdsdevops \
  --admin-password 2tdsdevops@2024 \
  --location eastus \
  --public-ip-sku Standard \
  --open-port 22,80

===============================================================================
PASSO 3: OBTER O IP PÚBLICO
===============================================================================

VIA PORTAL:
1. Acesse sua VM no portal
2. Copie o "Public IP address" da overview

VIA CLI:
az vm show -d -g rg-checkpoint1-2tds -n vm-checkpoint1-2tds-rmxxxx --query publicIps -o tsv

===============================================================================
PASSO 4: ACESSO SSH
===============================================================================

LINUX/MAC:
ssh tdsdevops@<PUBLIC_IP_ADDRESS>

WINDOWS (PowerShell):
ssh tdsdevops@<PUBLIC_IP_ADDRESS>

WINDOWS (PuTTY):
- Host: <PUBLIC_IP_ADDRESS>
- Port: 22
- Username: tdsdevops
- Password: 2tdsdevops@2024

===============================================================================
PASSO 5: INSTALAÇÃO E CONFIGURAÇÃO DO NGINX
===============================================================================

Após conectar via SSH, execute os comandos:

# Atualizar sistema e instalar NGINX e curl
sudo apt-get -y update && sudo apt-get -y install nginx && sudo apt-get install curl -y

# Habilitar e iniciar NGINX
sudo systemctl enable nginx && sudo systemctl start nginx

# Verificar status do NGINX
sudo systemctl status nginx

# Testar o servidor localmente
curl -m 80 localhost

# Testar externamente (substitua pelo IP público da sua VM)
curl -m 80 <PUBLIC_IP_ADDRESS>

===============================================================================
PASSO 6: VERIFICAÇÃO FINAL
===============================================================================

1. TESTE VIA NAVEGADOR: 
   Acesse http://<PUBLIC_IP_ADDRESS> - deve exibir a página padrão do NGINX

2. TESTE VIA CURL: 
   Execute curl http://<PUBLIC_IP_ADDRESS> - deve retornar o HTML da página

3. VERIFICAR PORTAS: 
   Confirme que as portas 22 e 80 estão abertas no Network Security Group

===============================================================================
COMANDOS DE VERIFICAÇÃO ÚTEIS
===============================================================================

# Status dos serviços
sudo systemctl status nginx
sudo systemctl status ssh

# Verificar portas em uso
sudo netstat -tulpn | grep :80
sudo netstat -tulpn | grep :22

# Logs do NGINX
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log

===============================================================================
TROUBLESHOOTING COMUM
===============================================================================

SSH NÃO CONECTA:
- Verifique se a porta 22 está aberta no NSG
- Confirme o IP público correto
- Teste conectividade: telnet <IP> 22

NGINX NÃO RESPONDE:
- Verifique se o serviço está rodando: sudo systemctl status nginx
- Confirme se a porta 80 está aberta no NSG
- Reinicie o serviço: sudo systemctl restart nginx

ERRO DE AUTENTICAÇÃO:
- Confirme username: tdsdevops
- Confirme senha: 2tdsdevops@2024
- Verifique se não há caracteres especiais incorretos

===============================================================================
ESPECIFICAÇÕES DO CHECKPOINT
===============================================================================

Resource Group: rg-checkpoint1-2tds
Virtual Machine name: vm-checkpoint1-2tds-rmxxxx
Region: US East US
Imagem: Ubuntu
Size: Standard_B1s
Usuário: tdsdevops
Senha: 2tdsdevops@2024
Public inbound ports: 22 e 80

Comandos de instalação:
sudo apt-get -y update && sudo apt-get -y install nginx && install curl -y
curl -m 80 <PublicIPAddress>
sudo systemctl enable nginx && systemctl start nginx

===============================================================================
CONCLUSÃO
===============================================================================

Seguindo estes passos, você terá uma VM Ubuntu funcional na Azure com NGINX 
instalado e acessível via web. A configuração atende às especificações do 
checkpoint com nomenclatura padronizada e portas adequadamente configuradas 
para acesso SSH e HTTP.

===============================================================================
PONTOS IMPORTANTES
===============================================================================

- Substitua "xxxx" no nome da VM pelo seu número de RM
- Anote o IP público da VM para acesso posterior
- Mantenha as credenciais seguras
- Verifique sempre os logs em caso de problemas
- O NGINX ficará disponível na porta 80 após a instalação

===============================================================================

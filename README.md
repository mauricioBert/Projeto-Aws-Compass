# Projeto: Infraestrutura na AWS

Este projeto tem como objetivo a criação de uma infraestrutura segura e escalável na AWS utilizando uma **VPC** (Virtual Private Cloud), com sub-redes públicas e privadas. A infraestrutura foi planejada para permitir a exposição de serviços ao público e manter serviços internos isolados da internet, garantindo maior segurança.

## Etapa 1: Criar uma VPC com Sub-redes Públicas e Privadas

Vamos criar uma **VPC** na faixa de rede `10.0.0.0/16`, que será dividida em duas sub-redes públicas e duas sub-redes privadas.

### 1.1. Configuração da VPC
![image](https://github.com/user-attachments/assets/55c881ff-9567-4f94-a54d-33a3d97b7b5c)

A VPC será configurada para utilizar a faixa de rede `10.0.0.0/16`. Esta faixa permite um amplo espaço para criar sub-redes, cada uma com sua própria faixa de IP, garantindo a separação entre os ambientes públicos e privados.

### 1.2. Sub-redes Públicas

As **sub-redes públicas** serão configuradas para hospedar serviços que precisam ser acessíveis pela internet. Elas receberão endereços dentro da faixa de rede `10.0.0.0/20` e `10.016.0/20`, por exemplo. Nesses sub-redes, será possível expor serviços como servidores web (Nginx, Apache) ou outros recursos acessíveis publicamente.

### 1.3. Sub-redes Privadas

As **sub-redes privadas** são destinadas a serviços internos que não precisam de acesso direto à internet, como bancos de dados, servidores de aplicação, etc. Elas ficarão nas faixas de rede `10.0.128.0/20` e `10.0.144.0/20`, garantindo que os recursos internos fiquem protegidos e não expostos diretamente.

![image](https://github.com/user-attachments/assets/be0408bd-fa93-4936-a69d-e4b93d093783)
---

Essa estrutura de VPC ajuda a garantir a segurança, ao mesmo tempo que proporciona flexibilidade e escalabilidade para os serviços implantados.

## Etapa 2: Criar Security Group para a EC2

Foi criado um **Security Group** para a instância EC2 com as seguintes regras de acesso.

### Regras de Entrada (Inbound Rules)

- **HTTP (porta 80)** → Aberto para `0.0.0.0/0` (acesso público ao site).
- **SSH (porta 22)** → Restrito ao IP do administrador (para acesso seguro via SSH).

### Regras de Saída (Outbound Rules)

- **HTTP (porta 80)** → Aberto para `0.0.0.0/0` (acesso público ao site).

![image](https://github.com/user-attachments/assets/e8495bdb-856f-4870-ab89-650cb3762640)


Este **Security Group** garante que o servidor possa:
- Receber requisições HTTP (acesso público ao site).
- Ser gerenciado via SSH de forma segura, restringido ao IP do administrador.
- Se comunicar com serviços externos, permitindo o tráfego HTTP.

## Etapa 3: Configuração da Instância EC2

![image](https://github.com/user-attachments/assets/39a81e86-c5de-4a79-9a1a-ec2acb06003a)

Escolha a **AMI Amazon Linux 2**, recomendada por sua compatibilidade e suporte da AWS.

No tipo da instância, utilize **t2.micro**, ideal para testes e disponível no **Free Tier** da AWS. Para maior desempenho, escolha **t3.medium** ou versões superiores.

Adicione as **tags necessárias** para organizar e identificar a instância de forma eficiente.

![image](https://github.com/user-attachments/assets/788364f3-58fc-464e-8deb-b2592aafb57a)


### Passo 1: Criar as Chaves de Acesso
1. No console da AWS, ao criar uma instância EC2, selecione a opção para **criar uma nova chave**.
2. Nomeie a chave e faça o **download do arquivo .pem**.
3. Guarde o arquivo **.pem** de forma segura, pois ele será usado para acessar a instância via **SSH**.

### Passo 2: Associar a VPC Criada
1. Durante a configuração da instância EC2, na seção de **Rede (Network)**, selecione a **VPC** que você criou anteriormente.
2. Caso não tenha uma VPC criada, você pode criar uma nova durante o processo de configuração da instância.

### Passo 3: Associar o Security Group
1. Na seção de **Segurança (Security)**, selecione o **Security Group** que você criou para a sua instância EC2.

![image](https://github.com/user-attachments/assets/df6c8d33-899e-4b23-b8c9-0d919fb6dcba)

### Passo 4: Criar e Lançar a Instância EC2
1. Após configurar as opções anteriores, clique em **Launch Instance** para criar sua instância EC2.
2. A instância será criada com as configurações definidas, incluindo VPC, Security Group e chave de acesso.

Agora, sua instância EC2 está configurada e pronta para ser acessada.

## Acessando a Instância EC2 via SSH

Para acessar a sua instância EC2 da AWS via SSH, siga os passos abaixo:

1. Abra o **terminal** ou **prompt de comando** no seu computador.
   
2. Navegue até o diretório onde está o arquivo da chave privada `.pem` (por exemplo, `projetoaws1.pem`).
   
3. Digite o comando SSH, substituindo `SUA_CHAVE.pem` pela sua chave privada e `IP DA EC2` pelo endereço IP público da sua instância EC2:
   ```bash
   ssh -i SUA_CHAVE.pem ec2-user@IP DA EC2
   
![image](https://github.com/user-attachments/assets/c8dffdb2-e078-43c6-b99d-ca3d89077ca6)

# Configuração e Instalação do Nginx na AWS EC2

## Subir o servidor Nginx na EC2

1. Acesse sua instância EC2 via SSH:
    ```bash
    ssh -i projetoaws1.pem ec2-user@44.200.205.195
    ```

2. Atualize os pacotes da sua instância EC2:
    ```bash
    sudo yum update -y
    ```

3. Instale o Nginx:
    ```bash
    sudo amazon-linux-extras install nginx1.12 -y
    ```

4. Inicie o serviço do Nginx:
    ```bash
    sudo systemctl start nginx
    ```

5. Habilite o Nginx para iniciar automaticamente após reiniciar a instância:
    ```bash
    sudo systemctl enable nginx
    ```

6. Verifique se o Nginx está em execução:
    ```bash
    sudo systemctl status nginx
    ```

## Criar uma Página Simples em HTML

1. Acesse o diretório onde o Nginx serve os arquivos:
    ```bash
    cd /usr/share/nginx/html
    ```

2. Crie um arquivo `index.html`:
    ```bash
    sudo nano index.html
    ```

3. Adicione o seguinte conteúdo ao arquivo `index.html`:
    ```html
    <html>
        <head>
            <title>Meu Projeto na AWS</title>
            <style>
                body {
                    font-family: Arial, sans-serif;
                    text-align: center;
                    margin-top: 50px;
                }
                h1 {
                    color: #4CAF50;
                }
                p {
                    font-size: 20px;
                }
            </style>
        </head>
        <body>
            <h1>Bem-vindo ao meu Projeto na AWS</h1>
            <p>Este é um site simples servido por um servidor Nginx na minha instância EC2.</p>
        </body>
    </html>
    ```

4. Salve e saia do editor (CTRL + X, depois Y para salvar e Enter).

## Acessar o Site

Agora, se você acessar o endereço IP público da sua instância EC2 no navegador (por exemplo: http://44.200.205.195), você verá a página HTML criada.

### Observação

- Certifique-se de que a porta 80 (HTTP) está aberta no seu Security Group para permitir o acesso ao Nginx.
- Caso tenha configurado tudo corretamente, você verá a página com as informações do seu projeto exibidas.

Isso deve te dar uma página web simples funcionando no servidor Nginx na sua instância EC2!

# Configuração de Serviço Systemd e Monitoramento do Nginx

## Passo 3: Criar um Serviço Systemd para Reiniciar o Nginx Automaticamente

Para garantir que o Nginx reinicie automaticamente em caso de falha, crie um serviço systemd para gerenciar o processo:

1. Crie ou edite o arquivo de serviço:
    ```bash
    sudo nano /etc/systemd/system/nginx.service
    ```

2. Adicione o seguinte conteúdo:
    ```ini
    [Unit]
    Description=The Nginx HTTP and reverse proxy server
    After=network.target

    [Service]
    Type=forking
    PIDFile=/run/nginx.pid
    ExecStartPre=/usr/sbin/nginx -t
    ExecStart=/usr/sbin/nginx
    ExecReload=/usr/sbin/nginx -s reload
    ExecStop=/usr/sbin/nginx -s stop

    [Install]
    WantedBy=multi-user.target
    ```

3. Habilite e inicie o serviço:
    ```bash
    sudo systemctl enable nginx
    sudo systemctl start nginx
    ```

Isso garantirá que o Nginx reinicie automaticamente sempre que necessário.

## Etapa 3: Script de Monitoramento + Webhook

Agora, vamos criar um script que monitorará o status do site hospedado e enviará uma notificação via Webhook do Discord caso o site não esteja disponível.

### Passo 1: Criar o Script de Monitoramento

Crie um script Bash para verificar se o servidor está respondendo corretamente a requisições HTTP.

1. Crie o arquivo do script:
    ```bash
    sudo nano /usr/local/bin/monitor_site.sh
    ```

2. Adicione o seguinte código ao script:
    ```bash
    #!/bin/bash

    # URL do site para monitoramento
    URL="http://localhost"
    # Webhook do Discord
    WEBHOOK_URL="https://discord.com/api/webhooks/1353792510388076704/1K4M0rzr-SncGg4g6G9f3_Qi1O1lg0DI3KgQU4pM02X9dDs6woVO9ggNKtBsSS25-H6L"
    # Arquivo de log
    LOG_FILE="/var/log/monitoramento.log"

    # Função para enviar notificação para o Discord
    send_discord_notification() {
        curl -X POST -H "Content-Type: application/json" -d "{\"content\": \"O site está fora do ar!\"}" $WEBHOOK_URL
    }

    # Verificar se o site está respondendo
    if ! curl -s --head --request GET $URL | grep "200 OK" > /dev/null; then
        # Se o site não estiver disponível, enviar a notificação e registrar o log
        echo "$(date) - Site fora do ar!" >> $LOG_FILE
        send_discord_notification
    else
        # Caso contrário, registrar no log que o site está disponível
        echo "$(date) - Site está online." >> $LOG_FILE
    fi
    ```

3. Torne o script executável:
    ```bash
    sudo chmod +x /usr/local/bin/monitor_site.sh
    ```

### Passo 2: Configurar o Cron para Executar o Script a Cada 1 Minuto

Para garantir que o script seja executado automaticamente a cada minuto, use o cron.

1. Edite o crontab:
    ```bash
    sudo crontab -e
    ```

2. Adicione a seguinte linha para executar o script a cada 1 minuto:
    ```bash
    * * * * * /usr/local/bin/monitor_site.sh
    ```

Isso configura o cron para executar o script de monitoramento a cada 1 minuto.

## ETAPA 4: Teste de Monitoramento e Webhook

### Passo 1: Verificar a Resposta do Webhook no Script

Primeiro, no seu script, você deve estar enviando a solicitação para o Discord da seguinte forma:

```bash
curl -X POST -H "Content-Type: application/json" -d '{"content": "O site está fora do ar!"}' "URL DO WEBHOOK"
 ```
### Passo 2: Testar manualmente o webhook

Uma maneira fácil de testar se o webhook está funcionando é executando manualmente o comando `curl` que dispara o webhook. Você pode rodar isso diretamente no terminal:

```bash
curl -X POST -H "Content-Type: application/json" -d '{"content": "O site está fora do ar!"}' "URL DO WEBHOOK"
 ```
### Passo 3: Verificar os logs

Se o comando `curl` no script estiver sendo executado corretamente, ele deve registrar a tentativa de envio no arquivo de log. Verifique o arquivo de log para ver se há entradas relacionadas ao envio do webhook:

```bash
sudo cat /var/log/meu_script.log
 ```
### Passo 4: Confirmar no Discord

Verifique seu canal no Discord onde o webhook está configurado. Se o webhook estiver funcionando, a mensagem de alerta será postada nesse canal sempre que o site estiver fora do ar.

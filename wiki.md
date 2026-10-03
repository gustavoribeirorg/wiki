# Guia técnico de servidor, Termux, Nginx, PHP/MariaDB, Docker, Jekyll e Cloudflare

> Manual prático para hospedagem estática e dinâmica (Jekyll, ClassicPress/WordPress, FastAPI) no Android com Termux e servidores Linux, orquestração de contêineres com Docker Compose, banco de dados MariaDB, automação de túneis seguros na Cloudflare, gerenciamento via SSH, controle de versão com Git/GitHub e sincronização com Rsync.

---

<a id="tabela-de-conteudo"></a>

## Tabela de Conteúdo

- **[1. Conexão SSH e chaves](#conexao-ssh)**
  - [Como descobrir o nome de usuário do Termux](#descobrir-usuario)
  - [Acessar o Termux a partir do computador](#acesso-computador)
  - [Conectar do Termux a um servidor remoto](#conectar-remoto)
  - [Finalizar o serviço SSH](#finalizar-ssh)
  - [Gestão e reuso de chaves SSH](#gestao-chaves-ssh)
  - [Acesso SSH e chaves na Oracle Cloud](#adicionar-chave-oracle)
  - [Alterar o idioma do console Oracle Cloud](#idioma-painel-oracle)
- **[2. Servidores web (Python e Nginx)](#servidores-web)**
  - [Servidor HTTP simples em Python](#python-http)
  - [Execução contínua em máquinas virtuais (Oracle Cloud)](#persistência-servidor-python)
  - [Migração para o Nginx (recomendado)](#migracao-nginx)
  - [Diferença entre root e alias no Nginx](#nginx-root-alias)
  - [Como verificar os servidores e portas em execução](#verificar-portas)
- **[3. MariaDB, PHP e ClassicPress / WordPress](#stack-php-mariadb)**
  - [Instalação e execução do MariaDB no Termux](#mariadb-termux)
  - [PHP e PHP-FPM no Termux com Nginx](#php-fpm-termux)
  - [Certificados SSL/TLS para o PHP no Termux](#certificados-ssl-php)
  - [Compilação da extensão Imagick via phpize](#imagick-compilacao-termux)
  - [Cache de objeto persistente com Redis](#redis-object-cache)
  - [Instalação e configuração do ClassicPress](#instalacao-classicpress)
  - [Migração de domínio no ClassicPress / WordPress](#migracao-dominio-wordpress)
  - [Consultas SQL úteis e solução de erros MariaDB](#consultas-sql-mariadb)
  - [Ajustes em templates PHP e personalização](#templates-php-personalizacao)
- **[4. Compilação e envio do Jekyll](#fluxo-jekyll)**
  - [O que é o Bundler e o comando bundle install](#bundler-install)
  - [Para que serve o prefixo bundle exec](#bundle-exec)
  - [Como funciona a compilação do site](#compilacao-site)
  - [Fluxo completo de compilação e transferência](#fluxo-transferencia)
  - [Linkagem interna no Jekyll](#linkagem-jekyll)
  - [Compatibilidade do Jekyll em arquiteturas 32 bits](#jekyll-32bits)
  - [Redirecionamento de RSS do Jekyll para WordPress](#redirecionamento-rss)
- **[5. Docker e Docker Compose em servidores e VMs](#docker-compose)**
  - [Comandos fundamentais do Docker Compose](#docker-compose-comandos)
  - [Execução em segundo plano e persistência (restart)](#docker-compose-segundo-plano)
  - [Resolução de conflito de contêiner existente](#docker-conflito-container)
  - [Diagnóstico de Erro 404 da Cloudflare em contêineres](#docker-cloudflare-404)
- **[6. Imagens e scripts Python](#otimizacao-imagens)**
  - [Conversão com ImageMagick e correção de rotação EXIF](#conversao-imagemagick)
  - [Compatibilidade de scripts no Python 3.6 (subprocess.run)](#compatibilidade-python)
- **[7. Página 404 personalizada no Nginx](#pagina-404)**
  - [Configuração no nginx.conf](#config-404)
  - [Como testar se a página 404 está funcionando](#testar-404)
- **[8. Publicação com Cloudflare Tunnel](#cloudflare-tunnel)**
  - [Instalação do cloudflared no cliente (computador)](#instalacao-cloudflared)
  - [Instalação em Linux 32 bits (incompatibilidade i386 vs 386)](#instalacao-32bits)
  - [Testes rápidos sem domínio próprio (no celular)](#testes-rapidos-tunnel)
  - [Configuração com domínio próprio](#config-dominio-proprio)
  - [Gerenciamento de túneis via CLI](#gerenciamento-tunnels-cli)
  - [Rotear e desrotear subdomínios](#rotear-desrotear-subdominios)
  - [Rotas DNS obrigatórias (evitar erro NXDOMAIN)](#dns-obrigatorio)
  - [Cuidados ao reiniciar o túnel via SSH remoto](#perigo-reinicio-ssh)
- **[9. Segurança, WAF e bloqueio de robôs](#seguranca-waf)**
  - [Bloqueio na Cloudflare (recomendado para economizar recursos)](#bloqueio-cloudflare)
  - [Bloqueio de invasões e SQL injection no Nginx](#bloqueio-exploits-nginx)
- **[10. Sessões e persistência com Tmux](#execucao-continua)**
  - [Evitar a suspensão pelo Android](#evitar-suspensao-android)
  - [Gerenciamento de sessões com Tmux](#gerenciamento-tmux)
  - [Múltiplas janelas dentro da mesma sessão](#janelas-tmux)
- **[11. SSH remoto via Cloudflare](#ssh-remoto)**
  - [Configuração no servidor (Termux)](#ssh-config-servidor)
  - [Por que a conexão direta trava](#ssh-bloqueio-direto)
  - [Conectar a partir do Linux ou macOS](#ssh-linux-macos)
  - [Conectar a partir do Windows](#ssh-windows)
- **[12. Arquivos, Rsync e Git (SCP, Rsync e GitHub)](#gestao-arquivos-git)**
  - [Transferência com Rsync e SCP](#transferencia-rsync)
  - [Simulação de transferência sem alterar arquivos (dry-run)](#rsync-dry-run)
  - [Exibir data de modificação de arquivos com o comando ls](#ls-data-modificacao)
  - [Uso da barra no Rsync e SCP](#barra-rsync-scp)
  - [Autenticação e chaves SSH no GitHub](#autenticacao-github)
  - [Inicialização e repositório remoto](#git-inicializacao-remoto)
  - [Fluxo de trabalho diário e commits](#git-fluxo-trabalho)
  - [Gerenciamento de branches e merge](#git-branches-merge)
  - [Limpeza de cache, remoção e .gitignore](#git-cache-gitignore)
- **[13. Logs, GoAccess e IP real](#logs-goaccess)**
  - [1. Capturar o IP real no Nginx](#capturar-ip-real)
  - [2. Monitoramento de logs pelo terminal](#monitoramento-logs-terminal)
  - [3. Exibir logs no navegador](#exibir-logs-navegador)
  - [4. Painel visual interativo com GoAccess](#painel-goaccess)
- **[14. Aplicações Python (Termux, proot Debian e Uvicorn)](#proot-python-uvicorn)**
  - [Erros de compilação e criptografia no Termux nativo](#erros-criptografia-python-termux)
  - [Instalação e acesso ao proot-distro](#instalacao-proot)
  - [Limitações do Docker e proot](#docker-limite)
  - [Conflito de compilação (Bionic vs glibc)](#conflito-compilacao)
  - [Pacotes nativos APT e ambiente virtual](#pacotes-apt-venv)
  - [Uvicorn, portas e regras de rede](#uvicorn-portas-redes)
- **[15. Resolução de problemas](#solucao-problemas)**

---

<a id="conexao-ssh"></a>

## 1. Conexão SSH e chaves

O OpenSSH permite controlar o terminal do celular remotamente a partir de um computador na mesma rede local ou gerenciar servidores externos direto do celular.

<a id="descobrir-usuario"></a>
### Como descobrir o nome de usuário do Termux

No terminal do celular, execute um dos comandos abaixo para ver o identificador do seu usuário (normalmente algo como `u0_a229`):

```bash
whoami
# ou
id -un
```

<a id="acesso-computador"></a>
### Acessar o Termux a partir do computador

1. No Termux, instale o pacote OpenSSH e defina uma senha de acesso:
   ```bash
   pkg install openssh
   passwd
   ```
2. Descubra o endereço IP local do celular:
   ```bash
   ifconfig
   # ou
   ip a
   ```
3. Inicie o serviço do servidor SSH no celular:
   ```bash
   sshd
   ```
4. No terminal do computador, conecte-se especificando a porta padrão do Termux (`8022`):
   ```bash
   ssh u0_a229@192.168.3.33 -p 8022
   ```

<a id="conectar-remoto"></a>
### Conectar do Termux a um servidor remoto

Para gerenciar servidores externos diretamente a partir do celular:

```bash
ssh usuario@ip_do_servidor
# Caso utilize porta personalizada:
ssh usuario@ip_do_servidor -p numero_da_porta
```

<a id="finalizar-ssh"></a>
### Finalizar o serviço SSH

Para encerrar o servidor SSH no Termux quando terminar:

```bash
pkill sshd
```

<a id="gestao-chaves-ssh"></a>
### Gestão e reuso de chaves SSH

Para autenticação SSH, o servidor precisa da sua **chave pública** (normalmente salva com extensão `.pub`), enquanto a **chave privada** (ex: `.pem` ou `id_rsa`) permanece armazenada com o cliente que faz a conexão.

* **Uso em múltiplos computadores:** é possível copiar e usar a mesma chave privada em vários computadores (via pendrive ou armazenamento seguro). Após copiar para a pasta segura (`~/.ssh/` no Linux/macOS ou `C:\Users\seu_usuario\.ssh\` no Windows), é obrigatório ajustar as permissões no Linux/macOS com:
  ```bash
  chmod 600 ~/.ssh/sua_chave.pem
  ```
* **Conectar informando o arquivo da chave privada:** utilize o parâmetro `-i`:
  ```bash
  ssh -i ~/.ssh/sua_chave.pem usuario@IP_da_instancia
  ```
* **Boa prática de segurança:** embora reutilizar a chave seja possível, o ideal é criar um par de chaves próprio para cada dispositivo e adicionar as chaves públicas de todos eles no arquivo `~/.ssh/authorized_keys` do servidor. Se um dos computadores for extraviado ou comprometido, basta remover a respectiva chave pública do servidor sem afetar os demais acessos.

<a id="adicionar-chave-oracle"></a>
### Acesso SSH e chaves na Oracle Cloud

Ao criar uma instância na Oracle Cloud, o painel gera ou solicita uma chave pública. Para fazer login via SSH a partir de um computador, você precisa obrigatoriamente do arquivo da **chave privada** (extensão `.pem` ou `.key`), e não apenas da chave pública.

#### Cenário 1: Você está em um computador novo e não possui a chave privada

* **Opção 1 (Transferir a chave):** se você ainda possui acesso ao computador original onde a chave privada foi baixada, transfira o arquivo `.pem` para o computador atual (via pendrive, canal seguro ou e-mail protegido), salve em `~/.ssh/` e execute `chmod 600 ~/.ssh/sua_chave.pem`.
* **Opção 2 (Adicionar nova chave pública):** se você não tiver acesso à máquina antiga, gere um novo par de chaves no computador atual com `ssh-keygen -t rsa -b 4096` e insira a nova chave pública no servidor através do console da Oracle Cloud.

#### Métodos para inserir a nova chave pública na instância

**Método A: Via Console Serial / Cloud-init (Se perdeu totalmente o acesso SSH)**

1. No painel da Oracle Cloud, vá em **Compute > Instances** e selecione a máquina virtual.
2. Em **Console connection**, crie uma conexão de console da instância para abrir o terminal serial diretamente pelo navegador.
3. Alternativamente, verifique em **Instance details** o campo **Cloud-init script** em *Metadata* se a máquina permitir injeção de scripts na reinicialização.
4. Com a conexão do console serial aberta e após autenticar, adicione a nova chave pública ao arquivo de chaves autorizadas:
   ```bash
   echo "sua-nova-chave-publica-aqui" >> ~/.ssh/authorized_keys
   chmod 600 ~/.ssh/authorized_keys
   ```

**Método B: Via SSH direto (Se você já está conectado por outra máquina)**

1. No computador novo, gere o par de chaves se ainda não tiver: `ssh-keygen -t rsa -b 4096`.
2. Exiba o conteúdo da chave pública recém-criada: `cat ~/.ssh/id_rsa.pub`.
3. No terminal da máquina que já possui acesso ao servidor Oracle, abra o arquivo:
   ```bash
   nano ~/.ssh/authorized_keys
   ```
4. Cole o conteúdo da nova chave pública em uma linha ao final do arquivo, salve (Ctrl+O, Enter, Ctrl+X) e garanta as permissões:
   ```bash
   chmod 600 ~/.ssh/authorized_keys
   ```
5. Teste o acesso a partir do novo computador: `ssh usuario@IP_do_servidor`.

<a id="idioma-painel-oracle"></a>
### Alterar o idioma do console Oracle Cloud

Se o painel administrativo da Oracle Cloud estiver em inglês ou você desejar alternar para português (ou vice-versa):

* **Passo a passo em Português:**
  1. No canto superior direito da tela, clique no **ícone de perfil** (menu do usuário).
  2. Selecione **My profile** (ou **User settings**).
  3. Na seção de preferências (**Preferences**), localize o campo **Language** (ou **Console language**).
  4. Selecione **Português (Brasil)** ou o idioma desejado no menu suspenso.
  5. Salve as alterações ou recarregue a página para aplicar.

* **Steps in English:** Click the Profile icon in the top-right corner > Select *User settings* (or *My profile*) > Scroll to *Preferences* > Under *Language*, select *English* > Save and refresh.

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="servidores-web"></a>

## 2. Servidores web (Python e Nginx)

Para hospedar páginas estáticas no celular ou em máquinas virtuais, você pode utilizar o módulo HTTP nativo do Python para testes ou migrar para o Nginx para maior eficiência de memória e processamento.

<a id="python-http"></a>
### Servidor HTTP simples em Python

Adequado para testes rápidos de arquivos em uma pasta:

```bash
pkg install python
cd ~/meu-site
python -m http.server 8080 &
```

Para manter o servidor Python rodando em segundo plano sem travar o terminal (usando `nohup`):

```bash
cd ~/gustavoribeiro-net/_site && nohup python3 -m http.server 8080 > /dev/null 2>&1 &
```

Para encerrar o servidor Python executado via `nohup` ou em segundo plano: `pkill -f "http.server"`.

<a id="persistência-servidor-python"></a>
### Execução contínua do servidor Python em máquinas virtuais (Oracle Cloud)

Ao fechar uma sessão SSH em uma VM (Virtual Machine) na Oracle Cloud ou em qualquer servidor Linux, a sessão encerra todos os subprocessos filhos. Para garantir que um servidor web Python continue rodando em segundo plano após desconectar, utilize uma das estratégias abaixo:

#### Opção 1: Usar nohup (simples e rápida)

O `nohup` ignora o sinal de encerramento da sessão (SIGHUP) enviado quando você sai do terminal:

```bash
nohup python3 app.py > output.log 2>&1 &
```

Pressione `Enter` para retornar ao terminal e digite `exit` para sair de forma segura. Para interromper o servidor posteriormente, consulte o número do processo com `pgrep -f app.py` e encerre com `kill ID_DO_PROCESSO`.

#### Opção 2: Usar Tmux (ideal para testes)

Cria uma sessão de terminal virtual independente que continua em execução na máquina virtual:

```bash
# Instalar o Tmux se necessário:
sudo apt update && sudo apt install tmux -y

# Criar e acessar a sessão:
tmux new -s webservice

# Iniciar o servidor Python:
python3 app.py
```

Para desconectar do terminal mantendo a aplicação rodando, pressione **`Ctrl + B`** e depois **`D`**. Para reconectar à sessão ao voltar à VM, execute `tmux attach -t webservice`.

#### Opção 3: Criar um serviço no systemd (recomendado para produção)

A abordagem definitiva para servidores em produção. Torna a aplicação um serviço gerenciado pelo sistema operacional, com suporte a inicialização automática no boot da VM e reinício automático em caso de falhas.

1. Crie o arquivo de configuração do serviço:
   ```bash
   sudo nano /etc/systemd/system/meuservidor.service
   ```
2. Cole o conteúdo abaixo ajustando o usuário, caminhos e arquivo principal da aplicação:
   ```ini
   [Unit]
   Description=Servidor Web Python
   After=network.target
   
   [Service]
   User=ubuntu
   WorkingDirectory=/caminho/para/seu/projeto
   ExecStart=/usr/bin/python3 /caminho/para/seu/projeto/app.py
   Restart=always
   
   [Install]
   WantedBy=multi-user.target
   ```
3. Recarregue o gerenciador do systemd, inicie e ative a inicialização no boot:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start meuservidor
   sudo systemctl enable meuservidor
   ```

> [!WARNING]
> **Atenção com a rede na Oracle Cloud:**
> 
> além de manter o processo ativo, certifique-se de liberar a porta da sua aplicação no firewall interno do sistema (como
> 
> `iptables`
> 
> ou
> 
> `ufw`
> 
> ) e adicionar uma regra de entrada (Ingress Rule) na Security List do painel administrativo da Oracle Cloud.

<a id="migracao-nginx"></a>
### Migração para o Nginx (recomendado)

O Nginx consome significativamente menos bateria e memória RAM do que o Python, processa arquivos estáticos com mais agilidade e gerencia cabeçalhos de proxy nativamente.

#### 1. Instalar o Nginx

```bash
pkg update && pkg install nginx -y
```

#### 2. Configurar a pasta e a porta

Abra a configuração principal com `nano $PREFIX/etc/nginx/nginx.conf` e ajuste o bloco `server`:

```nginx
server {
    listen 8080;
    server_name localhost;

    location / {
        root /data/data/com.termux/files/home/caminho-da-sua-pasta;
        index index.html;
    }
}
```

#### 3. Testar a sintaxe e iniciar o serviço

O comando `nginx -t` valida os arquivos de configuração antes da aplicação para evitar que o servidor caia por erros de escrita:

```bash
# Testar sintaxe:
nginx -t

# Iniciar o servidor:
nginx

# Recarregar após alterações de configuração:
nginx -s reload

# Parar o serviço:
nginx -s stop
```

<a id="nginx-root-alias"></a>
### Diferença entre root e alias no Nginx

A diferença essencial entre `root` e `alias` está na forma como o Nginx constrói o caminho final do arquivo no disco do servidor:

* **Diretiva `root`:** anexa todo o caminho da URL solicitada ao diretório base configurado.
* **Diretiva `alias`:** substitui o trecho da diretiva `location` pelo caminho configurado no disco.

| Diretiva | Exemplo de configuração | Requisição recebida | Caminho final buscado no disco |
| --- | --- | --- | --- |
| **root** | `location /imagens/ { root /var/www/site; }` | `/imagens/foto.png` | `/var/www/site/imagens/foto.png` |
| **alias** | `location /imagens/ { alias /var/www/site/; }` | `/imagens/foto.png` | `/var/www/site/foto.png` |

> [!WARNING]
> **Atenção à barra no alias:**
> 
> ao configurar a diretiva
> 
> `alias`
> 
> , inclua sempre a barra final (
> 
> `/`
> 
> ) tanto no bloco
> 
> `location /pasta/`
> 
> quanto no caminho de destino (
> 
> `alias /caminho/pasta/;`
> 
> ) para evitar erros de concatenação de arquivos.

#### Exemplo prático: site principal com subpasta de Wiki isolada

```bash
# Raiz do site principal
location / {
    root  /data/data/com.termux/files/home/gustavoribeiro-net/_site;
    index index.html index.htm;
}

# Subpasta independente usando alias para pasta separada no disco
location /wiki/ {
    alias /data/data/com.termux/files/home/wiki/;
    index index.html index.htm;
}

# Redirecionamento permanente para garantir a barra no final
location = /wiki {
    return 301 /wiki/;
}
```

<a id="verificar-portas"></a>
### Como verificar os servidores e portas em execução

* **Verificar processos ativos:** `ps aux | grep nginx` ou `ps aux | grep python`.
* **Listar portas em escuta:** `netstat -tuln` ou `ss -tuln` (procure por linhas com estado `LISTEN`).
* **Teste de resposta local via terminal:** `curl -I http://localhost:8080` (deve retornar cabeçalho `HTTP/1.1 200 OK` com `Server: nginx`).

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="stack-php-mariadb"></a>

## 3. MariaDB, PHP e ClassicPress / WordPress no Termux

Guia completo para hospedar aplicações dinâmicas baseadas em PHP (como ClassicPress e WordPress) e gerenciar bancos de dados relacionais MariaDB diretamente no Android através do Termux.

<a id="mariadb-termux"></a>
### Instalação e execução do MariaDB no Termux

O MariaDB pode ser executado localmente no Android sem necessidade de root. No Termux, há uma distinção fundamental entre os comandos:

* **`mariadbd-safe` (Servidor):** processo do daemon que inicia e mantém o serviço do banco de dados rodando em segundo plano, monitorando o processo para reinício automático em caso de encerramento inesperado.
* **`mariadb` (Cliente):** utilitário de terminal para se conectar interativamente ao servidor que já está ativo e executar consultas SQL.

#### 1. Instalar os pacotes

```bash
pkg update && pkg install mariadb php -y
```

#### 2. Iniciar o servidor MariaDB em segundo plano

```bash
mariadbd-safe &
```

Para confirmar se o processo está em execução: `pgrep mariadbd` (deve retornar o ID numérico do processo).

#### 3. Criar banco de dados, usuário e conceder privilégios

1. Acesse o console do MariaDB como administrador (sem senha inicialmente):
   ```bash
   mariadb -u root
   ```
   O prompt mudará para `MariaDB [(none)]>`.
2. Execute as instruções SQL para criar o banco de dados e o usuário com permissões completas:
   ```sql
   CREATE DATABASE meu_banco;
   CREATE USER 'meu_usuario'@'localhost' IDENTIFIED BY 'sua_senha_segura';
   GRANT ALL PRIVILEGES ON meu_banco.* TO 'meu_usuario'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```
3. Teste o acesso com o novo usuário criado:
   ```bash
   mariadb -u meu_usuario -p meu_banco
   ```
   Digite a senha quando solicitada. O prompt abrirá diretamente em `MariaDB [meu_banco]>`.

#### 4. Solução: "A mysqld process already exists" ou travamento de inicialização

Se o comando `mariadbd-safe &` fechar com erro indicando que o processo já existe, ou se o serviço cair de forma abrupta mantendo arquivos de trava (.pid) presos no disco:

```bash
# Encerra qualquer instância presa:
killall -9 mysqld 2>/dev/null

# Remove o arquivo PID retido:
rm -f $PREFIX/var/lib/mysql/*.pid

# Reinicia o servidor:
mariadbd-safe &
```

<a id="php-fpm-termux"></a>
### PHP e PHP-FPM no Termux com Nginx

Para processar requisições dinâmicas no Nginx, o PHP deve rodar através do gerenciador de processos **PHP-FPM**. No Termux, o PHP-FPM vem configurado por padrão para escutar em um **Unix Socket** local (`php-fpm.sock`), e não na porta de rede TCP 9000.

#### 1. Iniciar o PHP-FPM

```bash
php-fpm
```

O comando roda em segundo plano automaticamente. Verifique se o processo está ativo com `pgrep php-fpm`.

#### 2. Configurar o bloco PHP no Nginx (nginx.conf)

Adicione o bloco `location ~ \.php$` dentro do seu bloco `server { ... }`:

```nginx
location ~ \.php$ {
    fastcgi_pass unix:/data/data/com.termux/files/usr/var/run/php-fpm.sock;
    fastcgi_index index.php;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
}
```

Valide a sintaxe com `nginx -t` e recarregue com `nginx -s reload`.

#### 3. Solução de falhas comuns no PHP-FPM (Termux)

* **Diretório de execução ausente:** se o PHP-FPM fechar imediatamente, crie o diretório de sockets:
  ```bash
  mkdir -p $PREFIX/var/run
  ```
* **Diagnóstico em primeiro plano:** para ver o log exato do erro na tela:
  ```bash
  php-fpm -F
  ```
* **Erro "Another FPM instance seems to already listen on php-fpm.sock":** indica que o socket ficou preso da execução anterior:
  ```bash
  rm -f $PREFIX/var/run/php-fpm.sock && php-fpm
  ```
* **Erro "Bad system call" ao usar pkill:** as restrições do kernel Android no Termux barram o comando `pkill`. Encerre os processos usando o comando `kill` clássico:
  ```bash
  kill -9 $(pgrep php-fpm)
  # ou
  killall php-fpm
  ```
* **Porta 8080 em conflito (Address already in use):** se você tentar iniciar o PHP embutido (`php -S localhost:8080`) e a porta estiver ocupada pelo Nginx, use outra porta (`php -S localhost:8081`) ou identifique o processo ativo com `lsof -i :8080`.

<a id="certificados-ssl-php"></a>
### Certificados SSL/TLS para o PHP no Termux

Ao realizar requisições HTTPS externas (por exemplo, quando o WordPress/ClassicPress tenta verificar atualizações no repositório de plugins ou quando o cURL faz chamadas para APIs), o PHP pode disparar avisos como *"O ClassicPress não conseguiu estabelecer uma conexão segura com o WordPress.org"* ou erros de cURL 60 (SSL certificate problem).

Isso acontece porque o PHP do Termux não localiza os certificados raiz padrão do sistema. Para resolver:

1. Instale o pacote de certificados de autoridade:
   ```bash
   pkg update && pkg install ca-certificates -y
   ```
2. Crie um arquivo de configuração específico em `$PREFIX/etc/php/conf.d/cacert.ini` (pasta onde o PHP do Termux carrega diretivas complementares automaticamente):
   ```ini
   echo 'curl.cainfo = "/data/data/com.termux/files/usr/etc/tls/cert.pem"' > $PREFIX/etc/php/conf.d/cacert.ini
   echo 'openssl.cafile = "/data/data/com.termux/files/usr/etc/tls/cert.pem"' >> $PREFIX/etc/php/conf.d/cacert.ini
   ```
3. Reinicie o PHP-FPM para aplicar as diretivas:
   ```bash
   killall php-fpm && rm -f $PREFIX/var/run/php-fpm.sock && php-fpm
   ```

<a id="imagick-compilacao-termux"></a>
### Compilação da extensão Imagick via phpize

A biblioteca `Imagick` oferece recursos avançados de processamento de imagens para o PHP. Como o comando `pecl` e o pacote `php-pear` não existem nos repositórios oficiais do Termux (retornando *Unable to locate package php-pear*), a extensão deve ser compilada a partir do código-fonte utilizando o `phpize`:

1. Instale as bibliotecas do ImageMagick e o conjunto de compiladores:
   ```bash
   pkg install imagemagick clang make autoconf pkg-config git -y
   ```
2. Clone o repositório oficial do Imagick e compile o módulo:
   ```bash
   git clone https://github.com/Imagick/imagick.git
   cd imagick
   phpize
   ./configure
   make
   make install
   ```
3. Ative a extensão gerada nas configurações do PHP:
   ```ini
   echo 'extension=imagick.so' > $PREFIX/etc/php/conf.d/imagick.ini
   ```
4. Reinicie o PHP-FPM e valide se o módulo está carregado:
   ```bash
   killall php-fpm && rm -f $PREFIX/var/run/php-fpm.sock && php-fpm
   php -m | grep imagick
   ```

<a id="redis-object-cache"></a>
### Cache de objeto persistente com Redis

O **cache de objeto persistente** armazena consultas do banco de dados na memória RAM entre os acessos das páginas. Em vez de o CMS consultar o MariaDB a cada requisição, ele recupera as consultas prontas da memória, acelerando consideravelmente o tempo de resposta.

1. No Termux, instale o servidor Redis e a extensão PHP correspondente:
   ```bash
   pkg update && pkg install redis php-redis -y
   ```
2. Inicie o serviço do Redis em segundo plano:
   ```bash
   redis-server --daemonize yes
   ```
3. Valide a execução com um teste de ping:
   ```bash
   redis-cli ping
   ```
   O retorno deve ser **`PONG`**.
4. No painel do ClassicPress / WordPress, instale o plugin **Redis Object Cache** e clique em **Habilitar Cache de Objeto**. Ele se conectará automaticamente ao host local `127.0.0.1:6379`.

<a id="instalacao-classicpress"></a>
### Instalação e configuração do ClassicPress

O ClassicPress é um fork focado em estabilidade, velocidade e suporte a PHP clássico sem o editor de blocos pesado.

1. Acesse o diretório onde o site será hospedado e extraia os arquivos:
   ```bash
   curl -LO https://www.classicpress.net/latest.zip
   unzip latest.zip
   ```
2. Configure o arquivo `wp-config.php`:
   ```bash
   cp wp-config-sample.php wp-config.php
   nano wp-config.php
   ```
3. Preencha as credenciais do MariaDB:
   ```bash
   define( 'DB_NAME', 'meu_banco' );
   define( 'DB_USER', 'meu_usuario' );
   define( 'DB_PASSWORD', 'sua_senha_segura' );
   define( 'DB_HOST', '127.0.0.1' );
   ```
    > [!WARNING]
    > **Atenção obrigatória com DB_HOST no Termux:**
    > 
    > configure sempre
    > 
    > `define( 'DB_HOST', '127.0.0.1' );`
    > 
    > em vez de
    > 
    > `localhost`
    > 
    > . No Android, usar
    > 
    > `localhost`
    > 
    > faz o PHP procurar o socket Unix padrão do MySQL que frequentemente não está disponível no caminho tradicional do Linux, gerando o erro
    > 
    > *Error establishing a database connection*
    > 
    > .

4. Abra o navegador no endereço do seu servidor local (ex: `http://localhost:8080`) e conclua a instalação administrativa.

<a id="migracao-dominio-wordpress"></a>
### Migração de domínio no ClassicPress / WordPress

Ao migrar de um domínio de desenvolvimento (ex: `classicpress.gustavoribeiro.net`) para o domínio definitivo de produção (ex: `gustavoribeiro.net`), o site pode falhar ao carregar folhas de estilo CSS, scripts JS e imagens, gerando erros como `NS_ERROR_DOM_NETWORK_ERR` e avisos como *"A resource is blocked by OpaqueResponseBlocking"* no console do navegador.

Isso acontece porque o banco de dados armazena URLs absolutas que continuam apontando para o endereço antigo. Siga os três passos para regularizar:

#### Passo 1: Forçar as URLs no wp-config.php (Correção Imediata)

Insira no `wp-config.php` antes da linha `/* That's all, stop editing! */`:

```bash
define( 'WP_HOME', 'https://gustavoribeiro.net' );
define( 'WP_SITEURL', 'https://gustavoribeiro.net' );
```

Isso força o carregamento imediato do tema e dos scripts JS essenciais pelo domínio correto.

#### Passo 2: Substituir as URLs antigas no Banco de Dados

Para corrigir links internos e caminhos de imagens em posts e opções, utilize um dos métodos de Search & Replace:

* **Via WP-CLI (Terminal):**
  ```bash
  wp search-replace 'https://classicpress.gustavoribeiro.net' 'https://gustavoribeiro.net' --all-tables
  ```
* **Via Plugin (Painel Web):** instale o plugin **Better Search Replace**, informe a URL antiga e a nova, selecione todas as tabelas (especialmente `cp_posts` e `cp_options`) e execute a alteração.
* **Via comandos SQL no MariaDB:**
  ```sql
  UPDATE cp_options SET option_value = 'https://gustavoribeiro.net' WHERE option_name IN ('siteurl', 'home');
  UPDATE cp_posts SET post_content = REPLACE(post_content, 'https://classicpress.gustavoribeiro.net', 'https://gustavoribeiro.net');
  UPDATE cp_posts SET guid = REPLACE(guid, 'https://classicpress.gustavoribeiro.net', 'https://gustavoribeiro.net');
  ```

#### Passo 3: Limpeza completa de caches

Limpe o cache do navegador com `Ctrl + F5` (ou `Cmd + Shift + R` no Mac), purge o cache da Cloudflare se o domínio estiver atrás de proxy e limpe os plugins de cache do CMS.

<a id="consultas-sql-mariadb"></a>
### Consultas SQL úteis e solução de erros MariaDB

#### 1. Pesquisar posts contendo tags HTML (ex: <br>)

```sql
SELECT ID, post_title, post_content 
FROM cp_posts 
WHERE post_content LIKE '%<br%';
```

#### 2. Erro "ERROR 1046 (3D000): No database selected"

Ocorre ao tentar executar comandos SQL sem selecionar previamente a base de dados ativa (indicado por `[(none)]` no prompt). Selecione com:

```sql
SHOW DATABASES;
USE nome_do_banco;
```

#### 3. Banco de dados não aparece no SHOW DATABASES

Se ao listar os bancos aparecerem apenas `information_schema` e `test`, você se conectou ao MariaDB com um usuário anônimo ou sem privilégios. Verifique os dados em `wp-config.php` e conecte-se com o usuário correto:

```bash
mysql -u SEU_USUARIO -p
# Digite a senha cadastrada
```

<a id="templates-php-personalizacao"></a>
### Ajustes em templates PHP e personalização

#### 1. Correção de erros de sintaxe em loops de posts (archive.php e search.php)

Erros como *Parse error: syntax error, unexpected token "<", expecting "elseif" or "else" or "endif"* acontecem quando tags `<?php` são abertas consecutivamente sem fechar os blocos anteriores, ou quando instruções condicionais (como `else :`) ficam soltas fora das tags PHP ao intercalar código com HTML ou `<noscript>`.

Estrutura correta de template:

```php
<?php if ( have_posts() ) : ?>
    <?php
    while ( have_posts() ) :
        the_post();
        get_template_part( 'template-parts/content', get_post_type() );
    endwhile;
    ?>
    <noscript>
        <?php the_posts_pagination( array( 'mid_size' => 1 ) ); ?>
    </noscript>
<?php else : ?>
    <article class="no-results">
        <p><?php esc_html_e( 'Nada encontrado.', 'the-theme' ); ?></p>
    </article>
<?php endif; ?>
```

#### 2. Listar Páginas na barra lateral fora de Categorias

Para exibir uma listagem de páginas institucionais reaproveitando a formatação CSS da barra lateral:

```php
<?php
$pages = get_pages();
$current_page_id = get_queried_object_id();
?>
<?php if ( ! empty( $pages ) ) : ?>
    <span class="sidebar-title"><?php esc_html_e( 'Páginas', 'the-theme' ); ?></span>
    <ul class="category-list">
        <?php foreach ( $pages as $page ) : ?>
            <li>
                <a class="category-link<?php echo ( (int) $page->ID === (int) $current_page_id ) ? ' active' : ''; ?>"
                   href="<?php echo esc_url( get_permalink( $page->ID ) ); ?>">
                    <?php echo esc_html( $page->post_title ); ?>
                </a>
            </li>
        <?php endforeach; ?>
    </ul>
<?php endif; ?>
```

#### 3. Assunto dinâmico em links de e-mail (mailto) com título do post

Para incluir o título do artigo no assunto do link `mailto` sem que acentos ou espaços quebrem o link, utilize `get_the_title()` combinado com `rawurlencode()`:

```php
<address>
  <p>
    Você pode responder a esta publicação por
    <a href="mailto:contato@seusite.com?subject=<?php echo rawurlencode( 'Re: ' . get_the_title() ); ?>">e-mail.</a>
  </p>
</address>
```

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="fluxo-jekyll"></a>

## 4. Compilação e envio do Jekyll

O Jekyll é um gerador de sites estáticos que transforma arquivos em Markdown, layouts em Liquid e folhas de estilo Sass em páginas HTML prontas para publicação.

<a id="bundler-install"></a>
### O que é o Bundler e o comando bundle install

O Bundler é o gerenciador de dependências do ecossistema Ruby. Em vez de instalar gems manualmente uma a uma, o projeto define suas dependências em um arquivo chamado `Gemfile`.

* **Instalar as dependências do projeto:** execute na raiz da pasta do seu site:
  ```bash
  bundle install
  ```
  Esse comando lê o arquivo `Gemfile`, baixa as versões corretas de todas as gems necessárias (como `jekyll`, temas e plugins) e grava o arquivo `Gemfile.lock`.

<a id="bundle-exec"></a>
### Para que serve o prefixo bundle exec

Executar um comando diretamente como `jekyll build` pode causar conflitos se houver múltiplas versões do Jekyll ou de plugins instaladas no sistema. Ao usar `bundle exec`, você garante que o comando seja executado estritamente no contexto das versões declaradas no `Gemfile.lock` do projeto:

```bash
# Testar e visualizar localmente no computador com servidor de desenvolvimento:
bundle exec jekyll serve

# Compilar o site para publicação:
bundle exec jekyll build
```

<a id="compilacao-site"></a>
### Como funciona a compilação do site

Ao rodar `bundle exec jekyll build` no computador:

1. O Jekyll processa todos os arquivos Markdown, converte o código Sass em CSS puro e aplica os templates HTML.
2. O resultado final é salvo na pasta **`_site/`**, que contém apenas arquivos estáticos (HTML, CSS, imagens e JavaScript).
3. Essa pasta `_site` é a única que precisa ser transferida para o servidor no celular.

<a id="fluxo-transferencia"></a>
### Fluxo completo de compilação e transferência para o servidor

Passo a passo recomendado no terminal do computador:

1. Entre na pasta do projeto no computador:
   ```bash
   cd ~/meu-site
   ```
2. Gere a versão estática atualizada:
   ```bash
   bundle exec jekyll build
   ```
3. Transfira os arquivos gerados para o Termux via Rsync (inclua a barra `/` ao final da pasta de origem para enviar o conteúdo sem duplicar o nome do diretório):
   ```bash
   rsync -avz -e 'ssh -p 8022' _site/ u0_a229@IP_DO_CELULAR:~/gustavoribeiro-net/_site
   ```

<a id="linkagem-jekyll"></a>
### Linkagem interna no Jekyll

* **Uso da tag `link` (recomendado):** verifica se o arquivo realmente existe durante a compilação do site. Caso o arquivo seja renomeado ou movido, o Jekyll emite um erro, impedindo links quebrados.
  ```bash
  [Texto do link]({% link pagina.md %})
  [Texto do post]({% link _posts/2026-08-10-meu-post.md %})
  ```
* **Uso do filtro `relative_url`:** constrói a URL final com base no caminho relativo do site:
  ```bash
  [Texto do link]({{ '/minha-pagina/' | relative_url }})
  ```

<a id="jekyll-32bits"></a>
### Compatibilidade do Jekyll em arquiteturas 32 bits

Em sistemas antigos de 32 bits, o pacote `sass-embedded` (Dart Sass) falha por não possuir binários para 32 bits. A solução é fixar a versão 2.x do conversor no `Gemfile`:

```bash
# No Gemfile:
gem "jekyll-sass-converter", "~> 2.0"
```

Em seguida, atualize o pacote com o Bundler: `bundle update jekyll-sass-converter && bundle install`.

<a id="redirecionamento-rss"></a>
### Redirecionamento de RSS do Jekyll para WordPress

Ao migrar um blog do Jekyll para WordPress / ClassicPress, o endereço do feed RSS normalmente muda (por exemplo, de `/blog/rss.xml` para `/feed/`). Para não perder leitores e agregadores de conteúdo, configure um redirecionamento HTTP 301 (permanente):

#### Opções de implementação:

* **Via Nginx:** adicione a seguinte linha no bloco `server { ... }` do seu domínio:
  ```nginx
  rewrite ^/blog/rss\.xml$ /feed/ permanent;
  ```
* **Via Apache (.htaccess):** adicione no início do arquivo:
  ```bash
  Redirect 301 /blog/rss.xml https://seusite.com/feed/
  ```
* **Via Plugin no WordPress:** instale o plugin **Redirection** e crie uma rota com Origem `/blog/rss.xml`, Destino `/feed/` e Código 301.
* **Via Cloudflare:** crie uma regra em **Rules > Redirect Rules** apontando a URL antiga para a nova.

> [!WARNING]
> **Se o redirecionamento resultar em erro 404:**
> 
> 1. Certifique-se de que os **Links Permanentes** estão configurados no painel do WordPress em *Configurações > Links permanentes* (escolha "Nome do artigo").
> 2. No Nginx, garanta que a rota raiz possui a diretiva `try_files $uri $uri/ /index.php?$args;`.
> 3. Como alternativa direta sem links permanentes amigáveis, redirecione para o parâmetro nativo do feed:
>    ```nginx
>    rewrite ^/blog/rss\.xml$ /?feed=rss2 permanent;
>    ```

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="docker-compose"></a>

## 5. Docker e Docker Compose em servidores e VMs

Instruções para orquestração de contêineres com Docker Compose em máquinas virtuais Linux (Oracle Cloud, Ubuntu Server e Debian), execução contínua em segundo plano, persistência automática e resolução de conflitos.

<a id="docker-compose-comandos"></a>
### Comandos fundamentais do Docker Compose

Navegue até a pasta onde está localizado o arquivo `compose.yaml` (ou `docker-compose.yml`) para executar os comandos:

| Comando | Finalidade |
| --- | --- |
| `docker compose up` | Inicia os serviços exibindo a saída dos logs diretamente na janela do terminal (primeiro plano). |
| `docker compose up -d` | **Modo detached (segundo plano):** inicia os contêineres e libera o terminal imediatamente. |
| `docker compose up -d --build` | Recompila as imagens locais modificadas antes de subir os contêineres em segundo plano. |
| `docker compose ps` | Lista o status dos contêineres do projeto (em execução, parados ou reiniciando). |
| `docker compose logs` | Exibe o histórico recente dos logs de execução de todos os serviços. |
| `docker compose logs -f` | Acompanha os logs em tempo real (follow mode). Use `Ctrl + C` para sair sem parar o serviço. |
| `docker compose down` | Para e remove os contêineres e redes criados pelo projeto. |

<a id="docker-compose-segundo-plano"></a>
### Execução em segundo plano e persistência (restart)

Ao utilizar o parâmetro `-d` (detached mode), os contêineres continuam em execução contínua no daemon do Docker mesmo se você fechar o terminal ou desconectar da sessão SSH.

#### Como fazer o contêiner reiniciar sozinho após o reboot do servidor:

No arquivo `compose.yaml`, defina a propriedade `restart` no bloco de cada serviço:

```yaml
services:
  meu_servico:
    image: nginx
    restart: unless-stopped
    ports:
      - "80:80"
```

| Opção de restart | Comportamento |
| --- | --- |
| **unless-stopped** | Inicia automaticamente com o sistema operacional, a menos que você tenha parado o contêiner manualmente antes da reinicialização (**recomendado**). |
| **always** | Sempre reinicia o contêiner quando o sistema liga ou se o serviço cair por qualquer motivo. |
| **on-failure** | Reinicia apenas se o contêiner fechar com código de erro diferente de 0. |
| **no** | Não reinicia automaticamente (comportamento padrão). |

> [!WARNING]
> **Habilitar o Docker no boot do sistema:**
> 
> para que as diretivas de
> 
> `restart`
> 
> funcionem após a reinicialização da máquina, certifique-se de que o próprio daemon do Docker está habilitado no systemd do Linux:
> 
> ```bash
> sudo systemctl enable docker
> ```

<a id="docker-conflito-container"></a>
### Resolução de conflito de contêiner existente

Se ao executar `docker compose up -d` o terminal retornar o erro:

```bash
Error response from daemon: Conflict. The container name "/meu-app" is already in use by container "...". You have to remove (or rename) that container to be able to reuse that name.
```

Isso acontece porque um contêiner anterior com o mesmo nome permaneceu registrado no Docker (ativo ou encerrado com falha). Para resolver:

1. Force a remoção do contêiner conflitante:
   ```bash
   sudo docker rm -f meu-app
   ```
2. Confirme que o nome foi liberado:
   ```bash
   sudo docker ps -a | grep meu-app
   ```
   O comando não deve retornar nenhuma linha.
3. Suba o serviço novamente com rebuild:
   ```bash
   sudo docker compose up -d --build
   sudo docker compose ps
   ```

<a id="docker-cloudflare-404"></a>
### Diagnóstico de Erro 404 da Cloudflare em contêineres

Ao testar uma aplicação Docker exposta através de um subdomínio (ex: `curl -I subdominio.seusite.com`) e receber resposta `HTTP/1.1 404 Not Found` contendo o cabeçalho `Server: cloudflare`:

* O cabeçalho confirma que o DNS está resolvendo corretamente para a rede da Cloudflare, mas a Cloudflare não encontrou um destino registrado para responder por aquele subdomínio específico.

#### Causas mais frequentes e correção:

1. **Cloudflare Tunnel (cloudflared):** verifique no painel Zero Trust em *Networks > Tunnels > Seu Túnel > Public Hostname* se existe uma regra mapeando o subdomínio para a porta interna do contêiner (ex: `http://localhost:8080`). Se a regra não existir, a Cloudflare responderá com 404.
2. **Cloudflare Pages ou Workers:** se o subdomínio deveria apontar para um projeto no Pages ou Worker, o CNAME existe no DNS mas o subdomínio não foi adicionado na aba *Custom Domains* das configurações do projeto.
3. **Servidor de Origem / Proxy Reverso:** se o subdomínio aponta para o IP da sua VM com o proxy Cloudflare ativo (nuvem laranja), certifique-se de que o bloco `server_name` do Nginx ou Traefik aceita requisições para aquele subdomínio específico.

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="otimizacao-imagens"></a>

## 6. Imagens e scripts Python

Instruções para processamento e otimização automatizada de mídias para web e cuidados de retrocompatibilidade em interpretadores Python.

<a id="conversao-imagemagick"></a>
### Conversão com ImageMagick e correção de rotação EXIF

Ao converter imagens da câmera do celular (JPEG/PNG) para o formato moderno **WebP** via linha de comando ou scripts, as fotos tiradas na vertical podem aparecer rotacionadas de lado no navegador.

Isso acontece porque câmeras modernas gravam a orientação como um metadado EXIF em vez de girar a matriz de pixels real da imagem. O parâmetro `-auto-orient` faz o ImageMagick ler essa tag e ajustar a orientação dos pixels antes da compressão:

```bash
# Conversão individual com preservação de orientação e compressão otimizada:
magick input.jpg -auto-orient -quality 82 output.webp

# Em scripts legados de ImageMagick v6:
convert input.jpg -auto-orient -quality 82 output.webp
```

<a id="compatibilidade-python"></a>
### Compatibilidade de scripts no Python 3.6 (subprocess.run)

O argumento `capture_output=True` da função `subprocess.run()` foi introduzido apenas no Python 3.7. Em máquinas com Python 3.6 instalado (como CentOS 7 ou ambientes corporativos antigos), sua execução resulta em erro imediato:

```bash
TypeError: __init__() got an unexpected keyword argument 'capture_output'
```

#### Como reescrever a chamada para garantir compatibilidade com Python 3.6:

```python
import subprocess

# Incompatível com Python 3.6:
# resultado = subprocess.run(["magick", "..."], capture_output=True, text=True)

# Padrão compatível com Python 3.6, 3.7 e versões superiores:
resultado = subprocess.run(
    ["magick", "input.jpg", "-auto-orient", "-quality", "82", "output.webp"],
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE,
    universal_newlines=True
)
```

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="pagina-404"></a>

## 7. Página 404 personalizada no Nginx

Como configurar o Nginx para exibir uma página 404 estilizada própria em vez da página de erro crua padrão do servidor.

<a id="config-404"></a>
### Configuração no nginx.conf

1. Certifique-se de que o seu site gera um arquivo chamado `404.html` na pasta raiz de publicação (ex: `_site/404.html`).
2. Abra o arquivo de configuração do Nginx no Termux:
   ```bash
   nano $PREFIX/etc/nginx/nginx.conf
   ```
3. Adicione as seguintes diretivas dentro do bloco `server`:
   ```nginx
   server {
       listen 8080;
       server_name localhost;
   
       # Define a página para o código de erro 404
       error_page 404 /404.html;
   
       # Localização interna da página de erro com root explícito
       location = /404.html {
           root /data/data/com.termux/files/home/gustavoribeiro-net/_site;
           internal;
       }
   
       location / {
           root /data/data/com.termux/files/home/gustavoribeiro-net/_site;
           index index.html index.htm;
       }
   }
   ```
    > [!WARNING]
    > **Atenção à diretiva internal:**
    > 
    > a diretiva
    > 
    > `internal;`
    > 
    > impede que visitantes acessem a URL
    > 
    > `/404.html`
    > 
    > diretamente pelo navegador. Além disso, declarar
    > 
    > `root`
    > 
    > explicitamente dentro do bloco
    > 
    > `location = /404.html`
    > 
    > garante que o Nginx localize o arquivo mesmo se a requisição original vier de uma rota inexistente.

4. Valide as alterações e reinicie o servidor:
   ```bash
   nginx -t
   pkill nginx && nginx
   ```

<a id="testar-404"></a>
### Como testar se a página 404 está funcionando

No terminal do celular ou do computador, faça uma requisição para um arquivo que não existe:

```bash
curl -I http://localhost:8080/pagina-inexistente.html
```

A resposta deve conter o cabeçalho `HTTP/1.1 404 Not Found` e o corpo da resposta deve trazer o HTML da sua página personalizada.

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="cloudflare-tunnel"></a>

## 8. Publicação com Cloudflare Tunnel

O Cloudflare Tunnel permite expor servidores locais para a internet de forma segura, sem abrir portas no roteador residencial ou depender de IP público fixo.

<a id="instalacao-cloudflared"></a>
### Instalação do cloudflared no cliente (computador)

* **macOS (Homebrew):** instale diretamente com `brew install cloudflared`.
* **Linux 64 bits (Debian/Ubuntu/APT):**
  ```bash
  curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
  sudo dpkg -i cloudflared.deb
  ```
* **Windows:** instale via PowerShell com `winget install --id Cloudflare.cloudflared`.

<a id="instalacao-32bits"></a>
### Instalação em Linux 32 bits (incompatibilidade i386 vs 386)

A Cloudflare nomeia a arquitetura de 32 bits como `386` no instalador Debian, enquanto o Ubuntu/Debian exige `i386`. Se o comando `sudo dpkg -i` falhar com erro de arquitetura incompatível, você pode forçar a instalação ou instalar o executável compilado diretamente:

* **Opção A: forçar instalação do pacote .deb:**
  ```bash
  curl -L -o cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-386.deb
  sudo dpkg --force-architecture -i cloudflared.deb
  ```
* **Opção B: baixar o binário direto para a pasta do sistema:**
  ```bash
  curl -L -o cloudflared https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-386
  chmod +x cloudflared
  sudo mv cloudflared /usr/local/bin/
  cloudflared --version
  ```

<a id="testes-rapidos-tunnel"></a>
### Testes rápidos sem domínio próprio (no celular)

```bash
pkg install cloudflared
cloudflared tunnel --url http://localhost:8080
```

<a id="config-dominio-proprio"></a>
### Configuração com domínio próprio

1. Autentique no painel da Cloudflare:
   ```bash
   cloudflared tunnel login
   ```
2. Crie o túnel nomeado:
   ```bash
   cloudflared tunnel create meu-servidor
   ```
3. Crie o arquivo de regras em `nano ~/.cloudflared/config.yml`:
   ```yaml
   tunnel: SEU_ID_DO_TUNEL
   credentials-file: /data/data/com.termux/files/home/.cloudflared/SEU_ID_DO_TUNEL.json
   
   ingress:
     - hostname: seu-dominio.com
       service: http://localhost:8080
     - hostname: www.seu-dominio.com
       service: http://localhost:8080
     - service: http_status:404
   ```
4. Remova registros DNS antigos do tipo A e AAAA no painel da Cloudflare (mantendo intactos registros MX e TXT de e-mail).
5. Crie as rotas de DNS para o domínio:
   ```bash
   cloudflared tunnel route dns meu-servidor seu-dominio.com
   cloudflared tunnel route dns meu-servidor www.seu-dominio.com
   ```
6. Inicie o túnel:
   ```bash
   cloudflared tunnel run meu-servidor
   ```

<a id="gerenciamento-tunnels-cli"></a>
### Gerenciamento de tunnels via CLI

* **Listar túneis existentes:** `cloudflared tunnel list`
* **Deletar um túnel antigo ou quebrado:** `cloudflared tunnel delete NOME_OU_ID --force`
* **Localizar Token do túnel no painel Zero Trust:** acesse **Networks > Tunnels > Configure** e copie a chave longa após `--token`.
* **Executar túnel via Token:** `cloudflared tunnel run --token SEU_TOKEN`

<a id="rotear-desrotear-subdominios"></a>
### Rotear e desrotear subdomínios

#### Como rotear um novo subdomínio

1. Adicione a regra no arquivo `~/.cloudflared/config.yml` antes de `http_status:404`:
   ```bash
   - hostname: app.seu-dominio.com
       service: http://localhost:8080
   ```
2. Crie a rota no DNS:
   ```bash
   cloudflared tunnel route dns meu-servidor app.seu-dominio.com
   ```
3. Reinicie o túnel:
   ```bash
   pkill cloudflared && cloudflared tunnel run meu-servidor
   ```

<a id="desrotear-subdominio"></a>
#### Como desrotear um subdomínio

1. No painel da Cloudflare em **DNS > Records**, delete o registro CNAME apontado para o túnel.
2. Abra o arquivo `~/.cloudflared/config.yml` e apague as linhas do subdomínio.
3. Reinicie o túnel:
   ```bash
   pkill cloudflared && cloudflared tunnel run meu-servidor
   ```

<a id="dns-obrigatorio"></a>
### Rotas DNS obrigatórias (evitar erro NXDOMAIN)

Configurar o subdomínio apenas no arquivo `config.yml` define o encaminhamento interno da porta, mas **não publica o endereço na internet**. Para que o navegador consiga resolver o endereço, é obrigatório criar o apontamento DNS na Cloudflare:

```bash
cloudflared tunnel route dns nome-do-tunel subdominio.seu-dominio.com
```

Se você pular esta etapa, ferramentas como o `nslookup` retornarão o erro `NXDOMAIN` e o navegador exibirá que o servidor não pôde ser encontrado.

<a id="perigo-reinicio-ssh"></a>
### Cuidados ao reiniciar o túnel via SSH remoto

> [!WARNING]
> **Atenção:**
> 
> se a sua conexão SSH ativa passa pelo próprio túnel da Cloudflare (ex:
> 
> `ssh.seu-dominio.com`
> 
> ), executar comandos como
> 
> `pkill cloudflared`
> 
> encerra o túnel e derruba a sua própria sessão de forma irrecuperável. Qualquer manutenção no túnel deve ser feita na mesma rede Wi-Fi via IP local ou diretamente no celular.

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="seguranca-waf"></a>

## 9. Segurança, WAF e bloqueio de robôs

Servidores conectados à internet recebem constantes varreduras automáticas de robôs procurando falhas conhecidas, como diretórios de WordPress ou arquivos PHP desatualizados. Em um site estático, essas requisições recebem código 404 (arquivo não encontrado) e não comprometem o sistema.

<a id="bloqueio-cloudflare"></a>
### Bloqueio na Cloudflare (recomendado para economizar recursos)

Bloquear o tráfego malicioso nas bordas da Cloudflare evita que o processador do celular e a bateria sejam consumidos processando requisições inúteis.

1. No painel da Cloudflare, acesse seu domínio e vá em **Security > WAF > Custom Rules > Create Rule**.
2. Defina o nome da regra (ex: *Bloqueio de Varreduras PHP e WordPress*).
3. Em *Field*, selecione **URI Path**.
4. Em *Operator*, selecione **matches regex** ou **contains**.
5. Adicione termos como `wp-admin`, `wp-login`, `xmlrpc.php`, `.env`, `phpmyadmin`.
6. Em *Action*, selecione **Block** e clique em **Deploy**.

<a id="bloqueio-exploits-nginx"></a>
### Bloqueio de invasões e SQL injection no Nginx

Para proteger servidores Nginx diretamente na origem, adicione as regras padronizadas de bloqueio de exploits do Nginx Proxy Manager:

1. Baixe a lista de regras para a pasta do Nginx no Termux:
   ```bash
   curl -o $PREFIX/etc/nginx/block-exploits.conf https://raw.githubusercontent.com/NginxProxyManager/nginx-proxy-manager/refs/heads/develop/docker/rootfs/etc/nginx/conf.d/include/block-exploits.conf
   ```
2. Abra o arquivo `$PREFIX/etc/nginx/nginx.conf` e insira a diretiva dentro do bloco `server` do seu site:
   ```nginx
   server {
       listen 8080;
       server_name localhost;
   
       include /data/data/com.termux/files/usr/etc/nginx/block-exploits.conf;
   
       location / {
           root /data/data/com.termux/files/home/gustavoribeiro-net/_site;
           index index.html;
       }
   }
   ```
3. Valide e reinicie o servidor:
   ```bash
   nginx -t
   nginx -s reload
   ```

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="execucao-continua"></a>

## 10. Sessões e persistência com Tmux

O Android possui mecanismos agressivos de economia de bateria que encerram aplicativos em segundo plano. Para manter o Nginx, o túnel da Cloudflare e o servidor SSH ativos ininterruptamente, configure as permissões e o multiplexador de terminal **Tmux**.

<a id="evitar-suspensao-android"></a>
### Evitar a suspensão pelo Android

1. No Termux, execute o comando de trava de suspensão:
   ```bash
   termux-wake-lock
   ```
   Uma notificação permanente *"Termux (wake lock held)"* será exibida na barra de status.
2. Nas **Configurações do Android > Aplicativos > Termux > Bateria**, altere para **Sem restrições**.

<a id="gerenciamento-tmux"></a>
### Gerenciamento de sessões com Tmux

O Tmux cria sessões virtuais no terminal que continuam rodando mesmo se a janela for fechada ou o SSH desconectar:

* **Instalar o Tmux:** `pkg install tmux`
* **Criar sessão com nome:** `tmux new -s tunnel`
* **Desconectar da sessão (Detach):** pressione **`Ctrl + B`**, solte e aperte a tecla **`D`**.
* **Reconectar à sessão (Attach):** `tmux attach -t tunnel`
* **Listar sessões ativas:** `tmux ls`
* **Encerrar uma sessão:** `tmux kill-session -t tunnel`

<a id="janelas-tmux"></a>
### Múltiplas janelas dentro da mesma sessão

* **Criar nova janela:** `Ctrl + B` seguido de `C`.
* **Navegar para a próxima janela:** `Ctrl + B` seguido de `N`.
* **Navegar para a janela anterior:** `Ctrl + B` seguido de `P`.
* **Ir para janela específica pelo número:** `Ctrl + B` seguido do número (ex: `0`, `1`).

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="ssh-remoto"></a>

## 11. SSH remoto via Cloudflare

Procedimento para conectar com segurança ao terminal do celular a partir de qualquer lugar do mundo através do túnel da Cloudflare.

<a id="ssh-config-servidor"></a>
### Configuração no servidor (Termux)

1. Abra o arquivo de configuração do túnel em `nano ~/.cloudflared/config.yml`.
2. Adicione uma regra antes de `http_status:404` direcionando para o protocolo SSH na porta 8022:
   ```bash
   - hostname: ssh.seu-dominio.com
       service: ssh://localhost:8022
   ```
3. Crie a rota no DNS da Cloudflare:
   ```bash
   cloudflared tunnel route dns meu-servidor ssh.seu-dominio.com
   ```
4. Reinicie o túnel na sessão do Tmux.

<a id="ssh-bloqueio-direto"></a>
### Por que a conexão direta trava

Ao tentar rodar `ssh u0_a229@ssh.seu-dominio.com` sem configuração prévia, a conexão trava e exibe `Connection timed out`. A rede proxy da Cloudflare opera por padrão em tráfego HTTP/HTTPS e barra conexões brutas de TCP puro.

A solução é utilizar o `cloudflared` no computador cliente através do `ProxyCommand`, que encapsula os pacotes SSH em um túnel WebSocket seguro até o celular.

<a id="ssh-linux-macos"></a>
### Conectar a partir do Linux ou macOS

1. Instale o `cloudflared` no computador.
2. Edite o arquivo de configuração local em `nano ~/.ssh/config`:
   ```bash
   Host celular-remoto
       HostName ssh.seu-dominio.com
       User u0_a229
       Port 8022
       ProxyCommand cloudflared access ssh --hostname %h
   ```
3. Ajuste as permissões do arquivo:
   ```bash
   chmod 600 ~/.ssh/config
   ```
4. Conecte-se diretamente pelo apelido configurado:
   ```bash
   ssh celular-remoto
   ```

<a id="ssh-windows"></a>
### Conectar a partir do Windows

1. Baixe o binário para `C:\cloudflared\cloudflared.exe`.
2. Crie a pasta `.ssh` no seu usuário e edite o arquivo com `notepad C:\Users\SEU_USUARIO\.ssh\config`:
   ```bash
   Host celular-remoto
       HostName ssh.seu-dominio.com
       User u0_a229
       Port 8022
       ProxyCommand C:/cloudflared/cloudflared.exe access ssh --hostname %h
   ```
    > [!WARNING]
    > **Atenção às barras no Windows:**
    > 
    > utilize barras normais (
    > 
    > `/`
    > 
    > ) no caminho do
    > 
    > `ProxyCommand`
    > 
    > para evitar que o OpenSSH falhe com o erro
    > 
    > `CreateProcessW failed error:2`
    > 
    > .

3. Corrija as permissões do arquivo no PowerShell para evitar o erro *Bad permissions on .ssh/config*:
   ```bash
   icacls "$env:USERPROFILE\.ssh\config" /inheritance:r /grant:r "$($env:USERNAME):F"
   ```
4. Conecte-se pelo terminal:
   ```bash
   ssh celular-remoto
   ```

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="gestao-arquivos-git"></a>

## 12. Arquivos, Rsync e Git (SCP, Rsync e GitHub)

Procedimentos para sincronização de arquivos compilados, comportamento de barras na transferência, correção de erros no Rsync e controle de versão completo com Git e GitHub.

<a id="transferencia-rsync"></a>
### Transferência com Rsync e SCP

O Rsync compara os arquivos locais e remotos, transferindo apenas o que foi alterado para economizar largura de banda e tempo:

* **Enviar do computador para o Termux:**
  ```bash
  rsync -avz -e 'ssh -p 8022' ~/meu-site/_site/ u0_a229@IP_DO_CELULAR:~/gustavoribeiro-net/_site
  ```
* **Enviar para servidor remoto padrão (porta 22):**
  ```bash
  rsync -avz deepclassicpress/ ssh.gustavoribeiro.net:~/ClassicPress-release-2.7.2/wp-content/themes/gustavoribeiro-net/
  ```
* **Puxar do Termux para o computador:**
  ```bash
  rsync -avz -e 'ssh -p 8022' u0_a229@IP_DO_CELULAR:~/gustavoribeiro-net/_site/ ~/Downloads/site-backup
  ```
* **Ignorar arquivos já existentes sem atualizar:** adicione o parâmetro `--ignore-existing`.

> [!WARNING]
> **Atenção ao erro de uso do parâmetro `-e`:**
> 
> A opção `-e` serve estritamente para especificar o comando do protocolo de conexão remota (ex: `-e ssh` ou `-e 'ssh -p 8022'`). Colocar o caminho de uma pasta logo após a opção `-e` (ex: `rsync -avz -e pasta/ ...`) faz o rsync tentar executar essa pasta como um programa executável, disparando o erro:
> 
> ```bash
> rsync(1450): error: exec on 'pasta/': Permission denied
> rsync(1449): error: unexpected end of file
> rsync(1449): warning: child 1450 exited with status 14
> ```
> 
> **Correção:** Remova a opção `-e` (o rsync já utiliza SSH por padrão quando há dois pontos `:` no destino) ou especifique o protocolo de forma explícita com `-e ssh`.

<a id="rsync-dry-run"></a>
### Simulação de transferência sem alterar arquivos (dry-run)

Para testar e verificar quais arquivos seriam transferidos sem realizar qualquer modificação real no servidor de destino, adicione a opção `-n` (ou `--dry-run`):

```bash
rsync -avzn deepclassicpress/ ssh.gustavoribeiro.net:~/ClassicPress-release-2.7.2/wp-content/themes/gustavoribeiro-net/
```

O terminal exibirá a lista completa de arquivos que seriam copiados ou modificados, permitindo validar caminhos e permissões antes de aplicar a transferência definitiva.

<a id="ls-data-modificacao"></a>
### Exibir data de modificação de arquivos com o comando ls

Para conferir se os arquivos foram atualizados recentemente no servidor ou na máquina local, utilize os seguintes parâmetros do comando `ls`:

* **Formato longo com datas:** `ls -l`
* **Ordenar por data de modificação (mais recentes primeiro):** `ls -lt`
* **Ordenar por data na ordem inversa (mais antigos primeiro):** `ls -ltr`
* **Formato padronizado ISO (AAAA-MM-DD HH:MM):** `ls -l --time-style=long-iso`

<a id="barra-rsync-scp"></a>
### Uso da barra no Rsync e SCP

O uso da barra (`/`) no final do caminho afeta diretamente a forma como diretórios e arquivos são criados no destino.

#### 1. Comportamento da barra no caminho de origem

* **No Rsync:**
  * **Sem barra (`pasta`):** copia a pasta inteira (cria o diretório `pasta` dentro do destino).
  * **Com barra (`pasta/`):** copia apenas o conteúdo interno direto para o destino, sem recriar a pasta principal. Regra prática: a barra significa *"entrar na pasta e transferir o que está dentro"*.

* **No SCP:**
  * O comando `scp -r` sempre copia a pasta inteira, independentemente de colocar ou não a barra no final. Ambos os formatos geram o mesmo resultado.

#### 2. Transferência sem o parâmetro recursivo (-r ou -a)

* **No SCP:** cancela a operação imediatamente com mensagem de erro informando que o item é um diretório.
* **No Rsync:** ignora o diretório silenciosamente ou exibe o aviso `skipping directory`. É necessário usar `-r` ou `-a` (que já inclui a recursividade automaticamente).

#### 3. Enviar apenas o conteúdo interno usando o SCP

Para transferir somente os arquivos internos sem recriar a pasta raiz com o SCP, use o caractere coringa `/*` na origem:

```bash
# Apenas arquivos da raiz da pasta:
scp pasta/* usuario@servidor:/caminho/destino/

# Se houver subpastas internas (mantém a estrutura interna sem recriar a pasta principal):
scp -r pasta/* usuario@servidor:/caminho/destino/
```

> [!NOTE]
> **Atenção a arquivos ocultos:**
> 
> o coringa
> 
> `*`
> 
> ignora arquivos ocultos (que começam com
> 
> `.`
> 
> ). Para transferir arquivos ocultos sem duplicar o diretório raiz, o uso do Rsync com barra (
> 
> `rsync -avz pasta/ destino/`
> 
> ) é o método mais seguro.

#### 4. A barra na pasta de destino

* **Se o diretório de destino já existe:** tanto faz usar `destino` ou `destino/`; ambos recebem os arquivos corretamente.
* **Se o diretório de destino não existe (ao copiar arquivo único):**
  * **Com barra (`destino/`):** o sistema reconhece explicitamente que o caminho é uma pasta e evita erros.
  * **Sem barra (`destino`):** o comando cria um arquivo chamado `destino`, renomeando o arquivo enviado por engano em vez de guardá-lo em uma pasta.

<a id="autenticacao-github"></a>
### Autenticação e chaves SSH no GitHub

O GitHub exige chaves SSH criptográficas ou tokens de acesso pessoal no terminal em substituição a senhas convencionais.

#### Opção A: Chave SSH Ed25519 (Recomendada e moderna)

1. Abra o terminal e gere uma nova chave SSH usando seu e-mail do GitHub como etiqueta:
   ```bash
   ssh-keygen -t ed25519 -C "seu-email@dominio.com"
   ```
   Pressione `Enter` para aceitar o local padrão (`~/.ssh/id_ed25519`) e digite uma frase secreta (passphrase) segura se desejar.
2. **Fallback para sistemas legados:** se estiver utilizando um sistema muito antigo sem suporte a Ed25519, gere uma chave RSA de 4096 bits:
   ```bash
   ssh-keygen -t rsa -b 4096 -C "seu-email@dominio.com"
   ```
3. **Adicionar ao ssh-agent e Keychain (macOS):**
   ```bash
   ssh-add --apple-use-keychain ~/.ssh/id_ed25519
   ```
4. **Copiar a chave pública para a área de transferência:**
  * No macOS: `pbcopy < ~/.ssh/id_ed25519.pub`
  * No Linux / Termux: `cat ~/.ssh/id_ed25519.pub` (e copie o texto exibido)

5. **Cadastrar no GitHub:**
  1. No GitHub, clique na foto de perfil no canto superior direito > **Settings**.
  2. Na seção *Access* da barra lateral, clique em **SSH and GPG keys**.
  3. Clique em **New SSH key** (ou *Add SSH key*).
  4. No campo **Title**, insira um nome descritivo (ex: *"MacBook Pessoal"* ou *"Termux Android"*).
  5. No tipo de chave, selecione **Authentication Key** (ou *Signing Key* se for assinar commits).
  6. Cole a chave no campo **Key** e clique em **Add SSH key**.

6. **Testar a conexão com o GitHub:**
   ```bash
   ssh -T git@github.com
   ```
   O retorno deve confirmar a autenticação com sucesso: *"Hi usuario! You've successfully authenticated, but GitHub does not provide shell access."*
7. Altere a URL do repositório local para utilizar SSH:
   ```bash
   git remote set-url origin git@github.com:gustavoribeirorg/gustavoribeiro.net.git
   ```

#### Opção B: Personal Access Token (PAT)

1. No GitHub, acesse **Settings > Developer settings > Personal access tokens > Tokens (classic)**.
2. Gere um token com o escopo `repo` marcado e copie o código gerado.
3. Salve a credencial no terminal para não digitar sempre:
   ```bash
   git config --global credential.helper store
   ```

<a id="git-inicializacao-remoto"></a>
### Inicialização e repositório remoto

* **Iniciar repositório local:**
  ```bash
  git init
  ```
* **Clonar repositório existente:**
  ```bash
  git clone https://github.com/gustavoribeirorg/gustavoribeiro.net.git
  ```
* **Vincular repositório remoto:**
  ```bash
  git remote add origin https://github.com/gustavoribeirorg/gustavoribeiro.net.git
  ```
* **Visualizar URLs remotas cadastradas:**
  ```bash
  git remote -v
  ```

<a id="git-fluxo-trabalho"></a>
### Fluxo de trabalho diário e commits

* **Verificar status das alterações:**
  ```bash
  git status
  ```
* **Adicionar todas as alterações:**
  ```bash
  git add .
  ```
* **Criar um commit com mensagem:**
  ```bash
  git commit -m "feat: atualiza layout e novas rotas"
  ```
* **Enviar alterações para a branch principal:**
  ```bash
  git push origin main
  ```
* **Atualizar o repositório local com alterações remotas:**
  ```bash
  git pull origin main
  ```

<a id="git-branches-merge"></a>
### Gerenciamento de branches e merge

* **Criar e alternar para uma nova branch:**
  ```bash
  git checkout -b feature/nova-secao
  ```
* **Listar branches locais:**
  ```bash
  git branch
  ```
* **Alternar entre branches existentes:**
  ```bash
  git checkout main
  ```
* **Mesclar uma branch na branch atual:**
  ```bash
  git merge feature/nova-secao
  ```
* **Deletar uma branch após a mesclagem:**
  ```bash
  git branch -d feature/nova-secao
  ```

<a id="git-cache-gitignore"></a>
### Limpeza de cache, remoção e .gitignore

* **Remover arquivo mantendo no disco (apenas do Git):**
  ```bash
  git rm --cached arquivo.ext
  ```
* **Limpeza completa do índice para aplicar o .gitignore:**
  ```bash
  git rm -r --cached .
  git add .
  git commit -m "chore: limpa arquivos rastreados ignorados pelo .gitignore"
  ```
* **Reverter alterações de um arquivo para o último commit:**
  ```bash
  git checkout -- arquivo.ext
  ```

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="logs-goaccess"></a>

## 13. Logs, GoAccess e IP real

Instruções para visualizar logs de acessos, configurar o IP real repassado pelo proxy da Cloudflare e montar um painel estatístico em tempo real com o GoAccess.

<a id="capturar-ip-real"></a>
### 1. Capturar o IP real no Nginx

Como todas as requisições passam pelos servidores da Cloudflare, os logs do Nginx gravariam apenas os IPs dos proxies da Cloudflare (geralmente faixas como `172.70.x.x` ou `108.162.x.x`) em vez do endereço dos visitantes.

Para registrar o IP real, o Nginx precisa ler o cabeçalho `CF-Connecting-IP` enviado pela Cloudflare:

1. Crie um arquivo com a lista dos blocos de IP oficiais da Cloudflare:
   ```bash
   nano $PREFIX/etc/nginx/cloudflare-ips.conf
   ```
2. Cole o bloco de configuração abaixo:
   ```bash
   # Lista de IPs oficiais da Cloudflare para restaurar o IP real
   set_real_ip_from 173.245.48.0/20;
   set_real_ip_from 103.21.244.0/22;
   set_real_ip_from 103.22.200.0/22;
   set_real_ip_from 103.31.4.0/22;
   set_real_ip_from 141.101.64.0/18;
   set_real_ip_from 108.162.192.0/18;
   set_real_ip_from 190.93.240.0/20;
   set_real_ip_from 188.114.96.0/20;
   set_real_ip_from 197.234.240.0/22;
   set_real_ip_from 198.41.128.0/17;
   set_real_ip_from 162.158.0.0/15;
   set_real_ip_from 104.16.0.0/13;
   set_real_ip_from 104.24.0.0/14;
   set_real_ip_from 172.64.0.0/13;
   set_real_ip_from 131.0.72.0/22;
   set_real_ip_from 2400:cb00::/32;
   set_real_ip_from 2606:4700::/32;
   set_real_ip_from 2803:f800::/32;
   set_real_ip_from 2405:b500::/32;
   set_real_ip_from 2405:8100::/32;
   set_real_ip_from 2a06:98c0::/29;
   set_real_ip_from 2c0f:f248::/32;
   
   real_ip_header CF-Connecting-IP;
   ```
3. Abra o arquivo principal em `nano $PREFIX/etc/nginx/nginx.conf` e adicione dentro do bloco `http { ... }`:
   ```bash
   include /data/data/com.termux/files/usr/etc/nginx/cloudflare-ips.conf;
   ```
4. Reinicie o Nginx: `nginx -t && nginx -s reload`.

<a id="monitoramento-logs-terminal"></a>
### 2. Monitoramento de logs pelo terminal

* **Acompanhar acessos em tempo real:**
  ```bash
  tail -f $PREFIX/var/log/nginx/access.log
  ```
* **Visualizar últimas 50 linhas:**
  ```bash
  tail -n 50 $PREFIX/var/log/nginx/access.log
  ```
* **Filtrar requisições de um IP específico:**
  ```bash
  grep "192.168.3.10" $PREFIX/var/log/nginx/access.log
  ```
* **Filtrar apenas requisições que retornaram erro (404, 500, 403):**
  ```bash
  awk '$9 ~ /^(4|5)/' $PREFIX/var/log/nginx/access.log
  ```
* **Limpar o histórico de logs:**
  ```bash
  > $PREFIX/var/log/nginx/access.log
  ```

<a id="exibir-logs-navegador"></a>
### 3. Exibir logs no navegador

Caso queira visualizar o log cru diretamente pelo navegador através de uma rota protegida por senha ou restrita:

1. Crie um link simbólico do arquivo de log para uma pasta pública do Nginx:
   ```bash
   ln -s $PREFIX/var/log/nginx/access.log /data/data/com.termux/files/home/gustavoribeiro-net/_site/meulog.txt
   ```
2. No bloco `server` do Nginx, force o tipo MIME como texto puro:
   ```nginx
   location = /meulog.txt {
       default_type text/plain;
   }
   ```
3. Acesse `https://seusite.com/meulog.txt`.

<a id="painel-goaccess"></a>
### 4. Painel visual interativo com GoAccess

O GoAccess transforma o arquivo de log bruto em um dashboard HTML completo com gráficos de visitantes únicos, páginas mais acessadas, sistemas operacionais e navegadores.

#### 1. Instalar o GoAccess

```bash
pkg install goaccess -y
```

#### 2. Gerar relatório estático em HTML

```bash
goaccess $PREFIX/var/log/nginx/access.log -o /data/data/com.termux/files/home/gustavoribeiro-net/_site/stats.html --log-format=COMBINED
```

Abra `https://seusite.com/stats.html` no navegador para visualizar o painel estático gerado.

#### 3. Dashboard dinâmico em tempo real via WebSocket

Para que o painel atualize os gráficos automaticamente sem recarregar a página, configure o GoAccess com WebSocket reverso:

1. No bloco `server` do Nginx, adicione a rota de proxy para o WebSocket:
   ```nginx
   location /ws {
       proxy_pass http://127.0.0.1:7890;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection "upgrade";
   }
   ```
2. Reinicie o Nginx: `nginx -s reload`.
3. Execute o GoAccess em tempo real dentro de uma janela do Tmux:
   ```bash
   goaccess $PREFIX/var/log/nginx/access.log -o /data/data/com.termux/files/home/gustavoribeiro-net/_site/stats.html --log-format=COMBINED --real-time-html --ws-url=wss://seusite.com/ws --port=7890
   ```

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="proot-python-uvicorn"></a>

## 14. Aplicações Python (Termux, proot Debian e Uvicorn)

Instruções para solucionar erros de compilação C/Rust no Termux nativo e executar aplicações web dinâmicas em Python (como FastAPI, Pydantic e Uvicorn) isoladas em ambiente Debian.

<a id="erros-criptografia-python-termux"></a>
### Erros de compilação e criptografia no Termux nativo

Ao tentar instalar dependências de um projeto via `pip install -r requirements.txt` no ambiente nativo do Termux em processadores ARM (especialmente de 32 bits), bibliotecas que dependem de extensões nativas em Rust e C (como `cryptography`, `pillow`, `bcrypt` e `pydantic-core`) costumam falhar com mensagens de erro como:

```bash
error: subprocess-exited-with-error
Target triple not supported by rustup: arm-unknown-linux-androideabi
Rust not found, installing into a temporary directory
ERROR: Failed to build 'pydantic-core' / 'cryptography'
```

#### Por que isso acontece:

* O instalador do pip tenta compilar o código em Rust a partir dos fontes e busca o compilador através do `rustup`, que não disponibiliza toolchains automáticas para a arquitetura `arm-unknown-linux-androideabi` do Android.
* O processo de compilação dessas bibliotecas exige grande quantidade de memória RAM, frequentemente travando o terminal do celular ou sendo encerrado pelo kernel do Android com a mensagem `Killed`.

#### Solução 1: Instalar os pacotes pré-compilados pelo repositório do Termux (Recomendado)

O gerenciador de pacotes `pkg` do Termux possui versões binárias pré-compiladas especificamente para a arquitetura do seu dispositivo:

```bash
pkg update
pkg install python-cryptography python-pillow python-bcrypt -y
```

Após instalar esses pacotes nativos, execute novamente o pip:

```bash
pip install -r requirements.txt
```

O pip detectará que as extensões nativas já existem no ambiente e baixará apenas os módulos em Python puro sem recompilar nada.

#### Solução 2: Compilar com compilador Rust nativo e --no-build-isolation

Se o pacote exigir compilação (ex: `pydantic-core` / `maturin`), instale o Rust nativo do Termux e desative o isolamento de build do pip para que ele enxergue as ferramentas do sistema operacional:

```bash
pkg install rust binutils -y
pip install --no-build-isolation -r requirements.txt
```

#### Dica: Limpar o cache corrompido do pip

Se o instalador continuar reutilizando builds incompletos ou corrompidos, limpe o cache antes da nova tentativa:

```bash
pip cache purge
# ou instale ignorando o cache:
pip install --no-cache-dir -r requirements.txt
```

<a id="instalacao-proot"></a>
### Instalação e acesso ao proot-distro

Quando uma aplicação exigir um conjunto complexo de pacotes impossível de compilar no Termux nativo, o `proot-distro` é a melhor solução para rodar uma distribuição Linux completa com espaço de usuário isolado e bibliotecas glibc padrão.

1. No terminal padrão do Termux, instale o gerenciador:
   ```bash
   pkg install proot-distro -y
   ```
2. Instale a distribuição Debian:
   ```bash
   proot-distro install debian
   ```
3. Acesse o ambiente do Debian:
   ```bash
   proot-distro login debian
   ```
4. Navegue até a pasta de usuário do Termux dentro do Debian:
   ```bash
   cd /data/data/com.termux/files/home/
   ```
   O sistema de arquivos do Termux permanece montado e acessível nesse caminho dentro do ambiente do Debian, permitindo manipular os mesmos arquivos e pastas de projetos sem duplicar dados.

<a id="docker-limite"></a>
### Limitações do Docker e proot

O Docker não roda nativamente no Android porque depende de recursos do kernel do Linux (como `cgroups` e `namespaces`) que não estão habilitados no sistema móvel. Como o `proot` roda sem privilégios reais de root, o daemon do Docker (`dockerd`) não consegue iniciar.

* **Substituto nativo:** o ambiente Debian via `proot-distro` funciona na prática como um contêiner leve para gerenciar dependências e pacotes sem sobrecarregar o sistema.
* **Controle de Docker remoto:** se houver um servidor ou máquina virtual externa, é possível instalar apenas o cliente do Docker no Termux e conectar via SSH:
  ```bash
  export DOCKER_HOST=ssh://usuario@ip-do-servidor
  ```

<a id="conflito-compilacao"></a>
### Conflito de compilação (Bionic vs glibc)

Ao compilar bibliotecas C/Rust (como `cffi`, `pynacl` ou `cryptography`) com o `pip` dentro do Debian no `proot`, o compilador pode puxar acidentalmente os cabeçalhos da biblioteca Bionic do Android localizados em `/data/data/com.termux/files/usr/include`, causando erros massivos de compilação (como `'strict' undeclared`, falha em macros de disponibilidade e declarações de tipos conflitantes).

Para corrigir o problema, limpe as variáveis de compilação herdadas do Termux antes de qualquer instalação no Debian:

```bash
unset CFLAGS CPPFLAGS LDFLAGS CPATH C_INCLUDE_PATH LIBRARY_PATH PKG_CONFIG_PATH
```

<a id="pacotes-apt-venv"></a>
### Pacotes nativos APT e ambiente virtual

Para evitar compilar bibliotecas complexas em processadores ARM ou dispositivos de 32 bits, instale os binários pré-compilados diretamente pelo repositório do Debian com o APT e configure o ambiente virtual para reaproveitá-los:

#### 1. Instalar bibliotecas essenciais no Debian

```bash
apt update
# Nota: no Debian, o pacote PyNaCl chama-se python3-nacl (sem o prefixo "py")
apt install python3-nacl python3-paramiko python3-pydantic python3-pil python3-yaml python3-cffi python3-dev build-essential libffi-dev libssl-dev -y
```

#### 2. Criar ambiente virtual com suporte a pacotes do sistema

O parâmetro `--system-site-packages` faz o ambiente virtual enxergar as bibliotecas pesadas já instaladas pelo APT, baixando via `pip` apenas dependências leves em código Python puro:

```bash
python3 -m venv --system-site-packages .venv
source .venv/bin/activate
pip install -r requirements.txt
```

<a id="uvicorn-portas-redes"></a>
### Uvicorn, portas e regras de rede

* **Endereço de escuta (`0.0.0.0`):** indica que o servidor aceita conexões de qualquer interface. Ele não deve ser digitado no navegador.
* **Acesso pelo próprio celular:** use exclusivamente `http://127.0.0.1:8000` ou `http://localhost:8000`.
* **Acesso por outro computador na mesma rede Wi-Fi:** use o endereço IP local do celular (ex: `http://192.168.3.33:8000`). Tentar abrir `127.0.0.1:8000` em outro computador resultará em conexão recusada, pois ele tentará conectar em si próprio.
* **Comportamento de teste com curl:** `curl -I` envia uma requisição do tipo `HEAD`. Se a sua rota aceitar apenas `GET`, o servidor responderá com `405 Method Not Allowed`, o que confirma que o servidor está ativo e funcionando. Para testar o conteúdo completo, use `curl http://127.0.0.1:8000`.

[↑ Voltar ao topo](#tabela-de-conteudo)

---

<a id="solucao-problemas"></a>

## 15. Resolução de problemas

Tabela de referência rápida para diagnóstico e resolução de erros comuns de rede, serviços, compilação e banco de dados:

| Mensagem ou sintoma | Causa provável | Ação corretiva |
| --- | --- | --- |
| `rsync: error: exec on 'pasta/': Permission denied` | Uso incorreto da opção `-e` no rsync com um diretório imediatamente após em vez do comando de shell remoto. | Remova a opção `-e` (o rsync já usa SSH por padrão) ou use `-e ssh` ou `-e 'ssh -p 8022'`. |
| `Conflict. The container name "/..." is already in use` | Contêiner antigo parado ou em execução já está ocupando o nome configurado no Docker Compose. | Execute `sudo docker rm -f nome-do-container` e suba novamente com `sudo docker compose up -d --build`. |
| `HTTP/1.1 404 Not Found (Server: cloudflare) em contêiner` | DNS aponta para a Cloudflare, mas a rota pública (Public Hostname) não foi configurada no túnel ou Pages. | Acesse o painel Zero Trust > Tunnels > Public Hostname e aponte o subdomínio para a porta local correta (ex: `http://localhost:8080`). |
| `mysqld_safe A mysqld process already exists` | O servidor MariaDB já está ativo ou um arquivo `.pid` antigo ficou preso no disco do Termux. | Verifique com `pgrep mariadbd`. Se travado: `killall -9 mysqld 2>/dev/null && rm -f $PREFIX/var/lib/mysql/*.pid && mariadbd-safe &`. |
| `Error establishing a database connection (ClassicPress/WordPress)` | O PHP não conseguiu conectar ao MariaDB. No Termux, o uso de `localhost` falha pela ausência do socket Unix padrão. | No `wp-config.php`, altere para `define( 'DB_HOST', '127.0.0.1' );` e confirme se o MariaDB está rodando com `pgrep mariadbd`. |
| `Bad system call ao executar pkill -f php-fpm` | Restrições de segurança do kernel Android / seccomp bloqueiam a chamada de sistema usada pelo comando `pkill`. | Encerre o processo usando `killall php-fpm` ou `kill -9 $(pgrep php-fpm)`. |
| `Another FPM instance seems to already listen on php-fpm.sock` | O arquivo de socket Unix do PHP-FPM permaneceu no disco após um encerramento anterior. | Exclua o socket com `rm -f $PREFIX/var/run/php-fpm.sock` e inicie o serviço novamente com `php-fpm`. |
| `O ClassicPress não conseguiu estabelecer conexão segura com WordPress.org` | O cURL e o OpenSSL do PHP no Termux não encontram a cadeia de certificados raiz de autoridade (CA). | Instale `pkg install ca-certificates -y` e adicione `curl.cainfo` e `openssl.cafile` em `$PREFIX/etc/php/conf.d/cacert.ini`. |
| `Unable to locate package php-pear (Imagick)` | O repositório PEAR/PECL não existe nos repositórios oficiais do Termux. | Instale `imagemagick clang make autoconf`, clone `https://github.com/Imagick/imagick.git` e compile manualmente via `phpize`. |
| `ERROR 1046 (3D000): No database selected` | Consulta SQL executada sem especificar o banco de dados ativo. | Execute `SHOW DATABASES;` e selecione o banco com `USE nome_do_banco;` antes de rodar os comandos `SELECT` ou `UPDATE`. |
| `OpaqueResponseBlocking / NS_ERROR_DOM_NETWORK_ERR após migração` | O tema e as imagens continuam apontando para a URL do domínio antigo no banco de dados. | Defina `WP_HOME` e `WP_SITEURL` no `wp-config.php` e execute search & replace no banco via WP-CLI ou SQL em `cp_posts` e `cp_options`. |
| `Página 404 ao redirecionar feed RSS /blog/rss.xml para /feed/` | Links permanentes desativados no WordPress ou Nginx sem regra de repasse de rotas amigáveis. | Ative links permanentes em Configurações, garanta `try_files $uri $uri/ /index.php?$args;` no Nginx ou aponte o redirect para `/?feed=rss2`. |
| `Target triple not supported by rustup / falha ao compilar cryptography ou pydantic` | O instalador do pip tenta compilar extensões em Rust para ARM 32-bit Android sem toolchain compatível. | Instale os pacotes pré-compilados nativos com `pkg install python-cryptography python-pillow python-bcrypt` ou use o ambiente Debian no proot. |
| `Aplicação Python para ao fechar a sessão SSH na VM` | A desconexão da sessão encerra os processos vinculados ao terminal. | Rode a aplicação com `nohup python3 app.py &`, execute dentro de uma sessão `tmux` ou configure um serviço no systemd (`/etc/systemd/system/meuservidor.service`). |
| `Servidor Python não responde via IP público na Oracle Cloud` | Porta bloqueada no firewall do SO ou nas regras da Security List. | Abra a porta utilizada no firewall interno da VM (`iptables`/`ufw`) e adicione uma regra de entrada (Ingress Rule) na Security List no painel da Oracle Cloud. |
| `Connection refused (porta 8022)` | Serviço SSH inativo ou endereço IP alterado no celular. | Execute `sshd` no Termux e confirme o IP com `ifconfig`. |
| `ssh: connect to host ssh.dominio.com port 22: Connection timed out (travado)` | A Cloudflare proxyou o tráfego e barrou conexões SSH diretas por TCP. | Instale o `cloudflared` no computador cliente e adicione o `ProxyCommand cloudflared access ssh --hostname %h` no `~/.ssh/config`. |
| `Error 1033 (Cloudflare)` | O processo `cloudflared` está desligado ou perdeu a conexão com os servidores da Cloudflare. | Verifique com `pgrep cloudflared` e reinicie o túnel via Tmux com `cloudflared tunnel run meu-site` ou via `--token`. |
| `Unable to reach the origin service (dial tcp 127.0.0.1:8080)` | O túnel está ativo, mas o Nginx ou servidor Python não está rodando na porta indicada. | Inicie o servidor web e confirme com `netstat -tuln` ou `curl -I http://localhost:8080`. |
| `An A, AAAA, or CNAME record with that host already exists` | Conflito com registros de hospedagens anteriores (ex: GitHub Pages). | Remova os registros A e AAAA antigos do domínio no painel da Cloudflare e repita o comando de rota. |
| `server can't find subdominio: NXDOMAIN / Endereço não encontrado` | A rota foi definida no arquivo `config.yml`, mas o registro CNAME não foi criado no DNS da Cloudflare. | Execute `cloudflared tunnel route dns nome-do-tunel subdominio.seu-dominio.com` no terminal do Termux. |
| `dpkg: erro ao processar cloudflared.deb: arquitetura (386) não combina (i386)` | O pacote oficial da Cloudflare usa o identificador `386` em vez do padrão `i386` do Debian/Ubuntu. | Execute `sudo dpkg --force-architecture -i cloudflared.deb` ou baixe o binário compilado diretamente para `/usr/local/bin/cloudflared`. |
| `Falha na conexão / conexão recusada (127.0.0.1:8000) no computador` | Tentativa de acessar o localhost a partir de um aparelho diferente daquele onde o servidor está rodando. | Acesse pelo endereço IP local do celular na rede Wi-Fi ou através da URL pública do Cloudflare Tunnel. |
| `Building wheel for cffi / 'strict' undeclared / versioning.h error` | Variáveis de compilação herdadas do Termux misturaram cabeçalhos Bionic do Android com a glibc do Debian no proot. | Execute `unset CFLAGS CPPFLAGS LDFLAGS CPATH PKG_CONFIG_PATH` e instale `python3-cffi` via APT. |
| `Unable to locate package python3-pynacl` | Nome incorreto do pacote no repositório oficial do Debian. | Instale utilizando o nome registrado no Debian: `apt install python3-nacl`. |
| `Queda do SSH após reiniciar o túnel remotamente` | O comando `pkill cloudflared` encerrou o próprio túnel que mantinha a sessão SSH aberta. | Evite reiniciar o túnel por sessões remotas dependentes dele; configure novas rotas pelo painel web Zero Trust ou use conexão física. |
| `Página 404 padrão do Nginx exibida em vez da personalizada` | Falta da diretiva `root` dentro de `location = /404.html`, arquivo inexistente ou falha na recarga do Nginx. | Declare `root` explicitamente dentro de `location = /404.html { internal; }`, confirme a existência do arquivo com `ls` e force o reinício com `pkill nginx && nginx`. |
| `TypeError: __init__() got an unexpected keyword argument 'capture_output'` | Script Python usando recurso do Python 3.7+ em interpretador Python 3.6. | Substitua `capture_output=True` por `stdout=subprocess.PIPE, stderr=subprocess.PIPE, universal_newlines=True`. |
| `Falha na compilação da gem sass-embedded (Dart Sass)` | Incompatibilidade do binário Dart Sass em processadores Linux 32-bit. | Fixe `gem "jekyll-sass-converter", "~> 2.0"` no Gemfile e rode `bundle update`. |
| `Imagens convertidas aparecem rotacionadas ("deitadas")` | ImageMagick ignorou os metadados de orientação EXIF da câmera. | Adicione o parâmetro `"-auto-orient"` no comando do ImageMagick antes da qualidade. |
| `Directory not empty (rmdir)` | Tentativa de apagar pasta que contém arquivos. | Utilize `rm -rf nome_da_pasta` para remoção completa. |
| `Directory does not exist / Invalid or unwritable path (GoAccess)` | Uso de barra inicial (ex: `/nome-pasta`) apontando para a raiz do Android. | Use `~/nome-pasta` ou `$HOME/nome-pasta` para referenciar o diretório de usuário do Termux. |
| `Unable to authenticate WebSocket (GoAccess)` | Tentativa de abrir WebSocket sem criptografia através do túnel HTTPS. | Configure o bloco `location /ws` com `proxy_pass` no Nginx e aponte o parâmetro `--ws-url=wss://seusite.com/ws`. |
| `Bad permissions on .ssh/config (Windows)` | OpenSSH rejeita arquivos com permissões abertas a outros usuários. | Execute `icacls "$env:USERPROFILE\.ssh\config" /inheritance:r /grant:r "$($env:USERNAME):F"` no PowerShell. |
| `CreateProcessW failed error:2 (Windows)` | Espaços no caminho ou barras invertidas no `ProxyCommand`. | Coloque o executável em `C:/cloudflared/cloudflared.exe` e declare o comando com barras normais (`/`). |
| `websocket: bad handshake (Cloudflare)` | Falta de autenticação no Cloudflare Access ou URL sem HTTPS. | Execute `cloudflared.exe access login https://seu-dominio.com` e valide as regras no painel Zero Trust. |

[↑ Voltar ao topo](#tabela-de-conteudo)

---


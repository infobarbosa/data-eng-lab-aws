# Ambiente de Engenharia de Dados (AWS EC2 + code-server)

Author: Prof. Barbosa<br>
Contact: infobarbosa@gmail.com<br>
Github: [infobarbosa](https://github.com/infobarbosa)

---

## 1. Visão Geral e Arquitetura

Bem-vindo ao laboratório prático de Engenharia de Dados! Este ambiente foi projetado para fornecer uma experiência profissional, rápida e padronizada utilizando uma instância **AWS EC2** (`m5.large`) equipada com **Docker**, executando um container com **code-server** (Visual Studio Code direto no navegador), **Python 3**, **PySpark**, **Java 21 Headless** e **Boto3**.

Todo o seu trabalho fica persistido no diretório `/home/ubuntu/workspace` da instância EC2, que é montado dentro do container em `/home/barbosa/project`. Dessa forma, mesmo que o container seja reiniciado, seu código e arquivos permanecem intactos no disco EBS.

### ⚠️ Alerta Crítico: Região e Gestão de Custos

> **ATENÇÃO - OBRIGATÓRIO:**
>
> 1. **Região Ativa:** Certifique-se sempre de que você está operando na região **Norte da Virgínia (`us-east-1`)** no topo direito do Console AWS. Todos os recursos do laboratório devem residir exclusivamente nessa região.
> 2. **Consumo de Créditos:** A instância EC2 `m5.large` e o volume EBS gp3 consomem créditos do seu laboratório AWS Academy.
> 3. **Desligamento Automático:** O AWS Academy desliga suas máquinas após o período da sessão (timeout de 4 horas). Ao encerrar sua aula ou sessão de estudos, garanta que a instância esteja parada (**Stopped**) ou aguarde o desligamento automático.
> 4. **NUNCA Recrie a Máquina:** Uma vez provisionada na Aula 1, você **não** deve executar o script de provisionamento novamente nas aulas seguintes: cada execução cria uma instância **nova e vazia**, com data e hora no nome. Siga as instruções do *Guia de Sobrevivência* para religar a máquina existente. Se precisar mesmo recriar, encerre a anterior (**Instance state** > **Terminate instance**) para não consumir créditos.

---

## 2. Passo a Passo: Aula 1 (Provisionamento Único)

Na sua primeira aula de laboratório, executaremos o script de provisionamento via **AWS CloudShell**. Este passo só precisa ser feito **uma única vez** no início do semestre.

### Passo 1: Abrir o AWS CloudShell

1. Faça login no Console da AWS através do seu painel do AWS Academy.
2. Certifique-se de que a região selecionada no canto superior direito é **N. Virginia (`us-east-1`)**.
3. No topo do console, clique no ícone do **AWS CloudShell** (`>_`) ao lado da barra de pesquisa, ou pesquise por "CloudShell" no menu de serviços.
4. Aguarde o terminal do CloudShell carregar.

### Passo 2: Executar o Script de Provisionamento

No terminal do CloudShell, copie e cole o comando "one-liner" abaixo e pressione `Enter`:

```bash
curl -sS https://raw.githubusercontent.com/infobarbosa/data-eng-lab-aws/main/launch-lab.sh | bash
```

O script realizará de forma totalmente automatizada:
- Descoberta da sua VPC padrão e Subnet pública.
- Criação e configuração do Security Group (`lab-data-eng-sg`) liberando as portas `8080` (IDE) e `22` (SSH).
- Consulta da AMI mais recente do Ubuntu Server 24.04 LTS.
- Lançamento de uma nova instância EC2 `lab-data-engineering-AAAA-MM-DD_HH_MI_SS` (sufixo com a data e hora da execução), `m5.large`, com 30 GB gp3 e perfil IAM educacional.
- Configuração do User Data (instalação do Docker e inicialização do container do code-server).
- Monitoramento até a instância estar ativa (`running`), exibindo o IP público de acesso.

### Passo 3: Acessar a IDE code-server

Ao final da execução, o terminal exibirá as seguintes informações:

```text
===================================================================
            AMBIENTE DE LABORATÓRIO PRONTO PARA USO!              
===================================================================
 Instância ID:     i-0123456789abcdef0
 IP Público:       54.xxx.xxx.xxx
 Link de Acesso:   http://54.xxx.xxx.xxx:8080
 Autenticação:     Passwordless (Acesso Direto - Sem Senha)
-------------------------------------------------------------------
 IMPORTANTE - TEMPO DE INICIALIZAÇÃO:
 O script User Data está instalando o Docker e baixando a imagem
 com o code-server e PySpark em segundo plano.
 -> Tempo estimado de inicialização do container: 3 a 5 minutos.
 Se a página web não responder imediatamente, aguarde 2 minutos e
 atualize o seu navegador (Ctrl+F5 ou Cmd+Shift+R).
===================================================================
```

1. Abra uma nova aba no seu navegador e cole o endereço: `http://<IP-PUBLICO>:8080`.
2. Caso a página apresente erro de conexão ("A conexão falhou" ou "Site não pode ser alcançado"), **aguarde de 3 a 5 minutos** para que o Docker conclua o download da imagem e inicialize o serviço.
3. O **code-server** iniciará diretamente na interface da IDE sem solicitar senha (modo passwordless ativado por padrão).
4. Pronto! Você está dentro do ambiente do VS Code completo no seu navegador.

---

## 3. Guia de Sobrevivência: Aulas 2, 3 e 4 (Retomada Sem Perda de Dados)

O ambiente AWS Academy possui uma política de inatividade que interrompe o laboratório após 4 horas de uso contínuo, colocando a sua instância no estado **Stopped** (Parada).

> 🚫 **PROIBIÇÃO EXPRESSA:**
> **NÃO execute o script `launch-lab.sh` novamente nas próximas aulas!**
> Se você executar o script novamente, ele criará uma instância **nova e vazia** (com outra data e hora no nome), que também consome créditos. Seu disco e seus dados já estão salvos na instância existente.

### Como Religar a Instância Existente:

1. Acesse o Console AWS na região **us-east-1**.
2. No campo de busca superior, digite **EC2** e clique no serviço.
3. No menu lateral esquerdo, clique em **Instances** (Instâncias).
4. Localize a sua instância chamada **`lab-data-engineering-<data e hora>`** (ela estará com status *Stopped*; se houver mais de uma, use a mais recente).
5. Selecione a caixinha ao lado da instância, clique no botão **Instance state** (Estado da instância) no menu superior e clique em **Start instance** (Iniciar instância).
6. Aguarde cerca de 1 minuto até o status mudar para **Running** (Em execução).

### Como Obter o Novo Endereço de Acesso:

> ℹ️ **Atenção:** No modelo de nuvem sem IP elástico, toda vez que uma máquina é parada e reiniciada, a AWS atribui um **novo endereço IP público**.

1. Com a instância `lab-data-engineering-<data e hora>` selecionada na lista, olhe no painel de detalhes abaixo.
2. Localize o campo **Public IPv4 address** (Endereço IPv4 público).
3. Copie esse endereço e acesse no navegador adicionando a porta `:8080`:
   ```text
   http://<NOVO_IP_PUBLICO>:8080
   ```
4. O code-server abrirá diretamente na IDE (modo passwordless habilitado).
5. Como o Docker está configurado com `--restart always`, ele iniciará automaticamente junto com o sistema operacional, e todos os arquivos em `/home/barbosa/project` estarão intactos.

---

## 4. Tutorial Completo: Conectando o Projeto ao GitHub via SSH

Para versionar seus projetos acadêmicos e garantir que seu código esteja seguro na nuvem do GitHub, utilizaremos autenticação via chave SSH criptografada por `ed25519`.

### 4.1. Abrir o Terminal Integrado no code-server

Na interface web do code-server:
- Clique no menu superior (três linhas horizontais no canto superior esquerdo).
- Selecione **Terminal** > **New Terminal** (ou pressione `Ctrl + ` ` ` / ``Cmd + ` ``).

### 4.2. Gerar o Par de Chaves SSH

No terminal do code-server, execute o comando abaixo substituindo pelo seu e-mail do GitHub:

```bash
ssh-keygen -t ed25519 -C "seu-email@exemplo.com"
```

1. Quando perguntar `Enter file in which to save the key (/home/barbosa/.ssh/id_ed25519):`, apenas pressione **Enter**.
2. Quando perguntar pela senha (`passphrase`), pressione **Enter** duas vezes para deixar sem senha (ou defina uma de sua preferência).

### 4.3. Copiar a Chave Pública Gerada

Exiba a chave pública gerada na tela:

```bash
cat ~/.ssh/id_ed25519.pub
```

Selecione todo o conteúdo impresso (começa com `ssh-ed25519` e termina com o seu e-mail) e copie para a área de transferência (`Ctrl+C` ou `Cmd+C`).

### 4.4. Cadastrar a Chave Pública no GitHub

1. Acesse sua conta no [GitHub](https://github.com).
2. Clique na sua foto de perfil no canto superior direito e selecione **Settings** (Configurações).
3. No menu lateral esquerdo, clique em **SSH and GPG keys**.
4. Clique no botão verde **New SSH key**.
5. No campo **Title**, coloque um nome descritivo (ex.: `AWS EC2 Lab Data Eng`).
6. No campo **Key type**, mantenha `Authentication Key`.
7. No campo **Key**, cole a chave que você copiou no terminal.
8. Clique em **Add SSH key**.

### 4.5. Testar a Conexão com o GitHub

No terminal do code-server, valide a comunicação segura com o GitHub:

```bash
ssh -T git@github.com
```

- Quando perguntar `Are you sure you want to continue connecting (yes/no/[fingerprint])?`, digite:
  ```bash
  yes
  ```
- Você deverá ver uma mensagem de sucesso semelhante a:
  ```text
  Hi usuario! You've successfully authenticated, but GitHub does not provide shell access.
  ```

### 4.6. Configurar Identidade do Git

Configure seu nome e e-mail que assinarão os commits:

```bash
git config --global user.name "Seu Nome Completo"
git config --global user.email "seu-email@exemplo.com"
git config --global init.defaultBranch main
```

### 4.7. Inicializar o Repositório do Seu Projeto

Navegue até a pasta de trabalho (já é a pasta padrão `/home/barbosa/project`):

```bash
cd /home/barbosa/project
git init
```

### 4.8. Criar o Arquivo `.gitignore` Mandatório

Nunca envie arquivos temporários, caches do Python ou senhas para o GitHub. Crie o arquivo `.gitignore` com as regras essenciais:

```bash
cat << 'EOF' > .gitignore
# Ambientes virtuais
venv/
.venv/
env/

# Caches e compilados do Python
__pycache__/
*.py[cod]
*$py.class

# Variaveis de ambiente e segredos
.env

# Configuracoes locais do editor
.vscode/
.idea/

# Arquivos de log e temporarios
*.log
.DS_Store
EOF
```

### 4.9. Conectar ao seu Repositório Remoto e Fazer o Primeiro Push

1. Crie um novo repositório vazio no seu GitHub (ex.: `meu-lab-data-eng`).
2. Copie a URL do repositório no formato **SSH** (ex.: `git@github.com:seu-usuario/meu-lab-data-eng.git`).
3. No terminal do code-server, vincule e envie o código:

```bash
# Adicionar todos os arquivos
git add .

# Realizar o primeiro commit
git commit -m "feat: configuracao inicial do ambiente de dados"

# Renomear branch principal para main
git branch -M main

# Conectar ao repositorio remoto do GitHub (substitua pela sua URL SSH)
git remote add origin git@github.com:seu-usuario/meu-lab-data-eng.git

# Enviar os arquivos para o GitHub
git push -u origin main
```

---

## 5. Dicas Úteis no code-server

- **Testando o PySpark:** Abra o terminal e digite `python3 -c "import pyspark; print(pyspark.__version__)"` para testar sua instalação.
- **Instalando novas bibliotecas:** Caso precise de pacotes Python adicionais durante os exercícios, você pode instalá-los a qualquer momento:
  ```bash
  pip3 install pandas duckdb matplotlib
  ```
- **Acessando o Sistema de Arquivos:** Todo arquivo criado dentro de `/home/barbosa/project` é gravado diretamente no disco EBS da EC2 (`/home/ubuntu/workspace`).

---

## Parabéns!

Você configurou com sucesso seu ambiente de Engenharia de Dados na nuvem AWS, com IDE moderna, persistência de dados em disco e versionamento profissional com GitHub! Bons estudos e ótimas análises!

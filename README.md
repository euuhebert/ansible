# Guia Completo de Uso do Ansible

Este repositório contém uma documentação prática e detalhada para configurar e utilizar o Ansible, uma poderosa ferramenta de automação de TI. Aqui você encontrará tudo o que precisa para gerenciar configurações e executar tarefas repetitivas em ambientes de infraestrutura.

> **Nota:** Esta documentação está em constante atualização. Verifique regularmente para obter novos conteúdos e melhorias.

---

## Sumário

- [Introdução ao Ansible](#introdução-ao-ansible)
- [Instalação do Ansible](#instalação-do-ansible)
  - [Instalação no Debian](#instalação-no-debian)
  - [Instalação em distribuições baseadas em Red Hat](#instalação-em-distribuições-baseadas-em-red-hat)
- [Boas Práticas Ansible](#boas-práticas-ansible)
- [Pré-Requisitos](#pré-requisitos)
- [Configurações de Acesso por Chave SSH](#configurações-de-acesso-por-chave-ssh)
- [Configurações do Ansible](#configurações-do-ansible)
- [Sintaxe e Comandos](#sintaxe-e-comandos)
  - [Módulo de usuários](#módulo-de-usuários)
  - [Módulo de pacotes](#módulo-de-pacotes)
  - [Gerenciamento de serviços](#gerenciamento-de-serviços)
  - [Reiniciar servidores](#reiniciar-servidores)
- [Playbooks](#playbooks)
---

## Introdução ao Ansible

O Ansible é uma ferramenta de automação open-source que facilita o gerenciamento de configurações e a execução de tarefas em servidores. Com ele, é possível gerenciar vários servidores com um único comando, ideal para DevOps, administração de sistemas e automação de infraestrutura.

---

## Instalação do Ansible

### Instalação no Debian
1. Atualize os pacotes:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
2. Instale o Ansible:
   ```bash
   sudo apt install ansible -y
   ```
3. Verifique a instalação:
   ```bash
   ansible --version
   ```

### Instalação em distribuições baseadas em Red Hat
1. Ative o repositório EPEL:
   ```bash
   sudo yum install epel-release -y
   ```
2. Instale o Ansible:
   ```bash
   sudo yum install ansible -y
   ```
3. Verifique a instalação:
   ```bash
   ansible --version
   ```

---

## Boas Práticas Ansible

- Mantenha seus playbooks organizados em diretórios separados.
- Utilize o controle de versão (como Git) para gerenciar alterações em seus arquivos.
- Utilize configurações reutilizáveis, como variáveis e roles.
- Prefira acessar as máquinas via chave SSH para maior segurança.

---

## Pré-Requisitos

- **Python 3** instalado nas máquinas controladas.
- **SSH** configurado nas máquinas controladas.
- Criação de um usuário específico para o Ansible:
  1. Criar o usuário `ansible`:
     ```bash
     sudo useradd -m ansible
     ```
  2. Conceder privilégios de `sudo`:
     ```bash
     sudo usermod -aG sudo ansible
     ```
  3. Configurar o `sudo` para não solicitar senha:
     ```bash
     sudo visudo
     ```
     Adicione no final do arquivo:
     ```bash
     ansible ALL=(ALL) NOPASSWD:ALL
     ```

---

## Configurações de Acesso por Chave SSH

1. **Gerar uma chave SSH no servidor de controle:**
   ```bash
   ssh-keygen
   ```
   - Salve no caminho padrão `~/.ssh/id_rsa`.
2. **Copiar a chave pública para os servidores remotos:**
   ```bash
   ssh-copy-id -i ~/.ssh/id_rsa.pub ansible@<IP_DO_SERVIDOR>
   ```
3. Verifique o acesso:
   ```bash
   ssh ansible@<IP_DO_SERVIDOR>
   ```

---

## Configurações do Ansible

1. Crie um arquivo de configuração:
   ```bash
   touch ~/.ansible.cfg
   ```
2. Adicione a configuração:
   ```ini
   [defaults]
   inventory=~/ansible/hosts.cfg
   ```
3. Crie o arquivo de inventário:
   ```bash
   touch ~/ansible/hosts.cfg
   ```
   Exemplo de conteúdo:
   ```ini
   [webservers]
   192.168.0.10
   192.168.0.11
   
   [databases]
   192.168.0.20
   ```

---

## Sintaxe e Comandos

### Módulo de usuários

1. **Criar usuário:**
   ```bash
   ansible -m user -a "name=teste state=present" all
   ```
   Saída esperada:
   ```json
   {
       "changed": true,
       "name": "teste",
       "state": "present"
   }
   ```

2. **Criar usuário com senha:**
   Instale o módulo `passlib`:
   ```bash
   pip3 install passlib
   ```
   Execute o comando:
   ```bash
   ansible all -m user -a "name=teste password={{ 'senha' | password_hash('sha512') }} state=present"
   ```

3. **Remover usuário:**
   ```bash
   ansible all -m user -a "name=teste state=absent"
   ```

---

### Módulo de pacotes

1. **Instalar um pacote:**
   ```bash
   ansible all -m package -a "name=nginx state=present"
   ```

2. **Atualizar pacotes:**
   ```bash
   ansible all -m apt -a "upgrade=dist update_cache=yes"
   ```

---

### Gerenciamento de serviços

1. **Reiniciar serviço:**
   ```bash
   ansible all -m service -a "name=nginx state=restarted"
   ```

2. **Parar serviço:**
   ```bash
   ansible all -m service -a "name=nginx state=stopped"
   ```

---

### Reiniciar servidores

1. **Reiniciar todos os servidores:**
   ```bash
   ansible all -m reboot -a "reboot_timeout=10"
   ```

---

### Playbooks

Os playbooks são arquivos YAML que descrevem tarefas a serem executadas em hosts remotos. Eles são a principal forma de automatizar tarefas complexas e sequenciais no Ansible. Aqui está um guia completo para preparar o ambiente e criar playbooks de maneira eficiente.

---

#### Preparando o Ambiente

> **Os passos a seguir são opcionais; você pode usar qualquer editor de texto para criar e editar seus playbooks, como `vim`, `nano` ou `VS Code`. Neste exemplo, configuraremos o VS Code para trabalhar com arquivos YAML.**

---

#### 1. Configurando o VS Code como IDE para Playbooks

1. **Instale a extensão _Remote - SSH_ da Microsoft no VS Code.**
   
2. **Configure a conexão SSH com o host remoto:**
   - Clique no ícone de **+** na extensão Remote - SSH.
   - Insira o nome do usuário e o IP da máquina onde o Ansible está instalado.  
     Exemplo:
     ```bash
     ssh user@<ip-do-host_remoto>
     ```
   - Escolha o diretório onde sua chave SSH está armazenada.  
     Exemplo:
     ```
     C:\Users\user\.ssh\config
     ```
   - Insira a senha do usuário no host remoto.

3. **Confirme a conexão.**  
   A máquina remota agora está conectada ao VS Code.

   > **Observação:** Se o host remoto não tiver a ferramenta `tar` instalada, a conexão pode falhar. Certifique-se de instalá-la antes de continuar:
   ```bash
   sudo apt install tar      # Em sistemas baseados em Debian
   sudo yum install tar      # Em sistemas baseados em Red Hat
   ```

4. **Instale a extensão _Ansible_ da Red Hat no VS Code.**
   - Após instalar, clique no botão **Get Started**.

---

#### 2. Configurando Ferramentas Complementares no Host Remoto

1. **Instale a ferramenta `ansible-creator` no terminal do host remoto:**
   ```bash
   python3 -m pip install ansible-creator --no-input
   ```

2. **Instale o `ansible-lint` para verificar seus playbooks:**
   ```bash
   python3 -m pip install --user ansible-lint
   ```

---

#### 3. VS Code Pronto para Gerenciar e Escrever Playbooks

Com as configurações acima, o VS Code estará preparado para criar, editar e validar playbooks de forma prática e eficiente.

---

### Exemplo Simples de Playbook

O exemplo a seguir instala o servidor web **Nginx** em máquinas categorizadas como `webservers`:

```yaml
- hosts: webservers
  tasks:
    - name: Instalar Nginx
      apt:
        name: nginx
        state: present
```

---

Para executar o playbook:

1. Salve o arquivo com a extensão `.yaml` ou `.yml`.  
   Exemplo: `instalar_nginx.yaml`.

2. Execute o comando no terminal do host de controle:
   ```bash
   ansible-playbook instalar_nginx.yaml
   ```

3. **Saída esperada:**
   ```bash
   PLAY [webservers] ************************************************

   TASK [Instalar Nginx] ********************************************
   changed: [192.168.0.10]
   changed: [192.168.0.11]

   PLAY RECAP *******************************************************
   192.168.0.10                : ok=1    changed=1    unreachable=0    failed=0
   192.168.0.11                : ok=1    changed=1    unreachable=0    failed=0
   ```

---

Com essa configuração, você pode criar playbooks complexos e aproveitar as vantagens do Ansible para gerenciar sua infraestrutura.

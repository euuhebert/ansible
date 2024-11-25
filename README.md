# Guia de Uso do Ansible

Este repositório contém uma documentação prática para configurar e utilizar o Ansible, uma ferramenta de automação de TI que simplifica o gerenciamento de configurações e a execução de tarefas repetitivas em ambientes de infraestrutura. Aqui você encontrará instruções detalhadas e boas práticas para instalação, configuração e execução de comandos no Ansible.

> **Nota:** Esta documentação está em constante atualização, então fique atento a novos conteúdos e melhorias.

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

---

## Introdução ao Ansible
O Ansible é uma ferramenta de automação open-source que facilita o gerenciamento de configurações e a execução de tarefas em servidores. Ele permite gerenciar de forma eficiente vários servidores com um único comando, tornando-o ideal para DevOps, administração de sistemas e automação de infraestrutura.

---

## Instalação do Ansible

### Instalação no Debian
Para instalar o Ansible em sistemas Debian ou Ubuntu, execute os seguintes comandos:

```bash
sudo apt update
sudo apt install -y ansible
```

### Instalação em distribuições baseadas em Red Hat
Para instalar o Ansible em sistemas baseados no Red Hat, como o CentOS ou Fedora, use:

```bash
sudo dnf install -y ansible
```

---

## Boas Práticas Ansible

### Pré-Requisitos
- Ter o **Python 3** instalado nas máquinas
- **SSH** instalado e configurado

### Passo 1: Criar usuário ansible em cada máquina alvo
1. Crie um usuário chamado `ansible` em cada máquina que será gerenciada.
2. Dê privilégios de `sudo` para este usuário.

### Passo 2: Configurar o sudo sem senha
Edite o arquivo `sudoers` para permitir que o usuário `ansible` execute comandos sem senha:

```bash
ansible ALL=(ALL) NOPASSWD:ALL
```

---

## Configurações de Acesso por Chave SSH

### Passo 1: Gerar uma chave SSH no servidor de controle
No servidor de controle, execute o comando abaixo para gerar uma chave SSH:

```bash
ssh-keygen
```

> Salve a chave no local padrão pressionando **Enter** quando solicitado.

### Passo 2: Copiar a chave pública para as máquinas remotas
Para cada máquina remota, use o comando:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ansible@ip_da_maquina_remota
```

### Passo 3: Configurar o SSH para autenticação somente por chave
Em cada máquina remota, edite o arquivo `/etc/ssh/sshd_config` e adicione:

```bash
Match User ansible
PasswordAuthentication no
```

### Passo 4: Reiniciar o serviço SSH
Após as alterações, reinicie o SSH:

```bash
sudo systemctl restart sshd
```

### Passo 5: Testar a conexão
No servidor de controle, teste a conexão SSH com cada máquina remota:

```bash
ssh ansible@ip_da_maquina_remota
```

---

## Configurações do Ansible

1. **Criar arquivo de configuração do Ansible**:
   ```bash
   touch .ansible.cfg
   ```

2. **Definir o caminho do arquivo de configuração**:
   ```bash
   ANSIBLE_CONFIG=/home/ansible/ansible.cfg ansible --version
   ```

3. **Criar arquivo de inventário `hosts.cfg`**:
   Exemplo de configuração:

   ```ini
   [webservers]
   cardeal.dominio.br || ip || alias

   [database]
   azulao.dominio.br || ip || alias

   [ubuntu]
   cardeal
   ```

4. **Validar o inventário**:
   ```bash
   ansible-inventory -i hosts.cfg --list --yaml
   ```

5. **Configuração do `ansible.cfg`**:
   ```ini
   [defaults]
   inventory=home/ansible/hosts.cfg
   ```

---

## Sintaxe e Comandos

### Listar módulos de documentação
```bash
ansible-doc --list
```

### Exemplo de uso do módulo `ping`
```bash
ansible -m ping cardeal -u ansible
```

### Exemplo de uso do módulo `user`
```bash
ansible -m user -a "name=user state=present" all
```

### Criando usuário nas máquinas
```bash
ansible -bK -m user -a "name=nome_usuario state=present" all
```

> Para definir o `become` como padrão, adicione ao `ansible.cfg`:

```ini
[privilege_escalation]
become = True


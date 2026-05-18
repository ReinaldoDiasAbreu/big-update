# 🚀 BigUpdate Stack

<p align="center">
  <img src="https://img.shields.io/badge/OS-BigLinux%20%7C%20Arch%20%7C%20Manjaro-blue?style=for-the-badge&logo=linux" alt="OS Suportados">
  <img src="https://img.shields.io/badge/Shell-Bash%205+-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white" alt="Bash">
  <img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="MIT License">
</p>

O **BigUpdate Stack** é um script de automação em Bash desenvolvido para sistemas **BigLinux**, **Manjaro** e derivados Arch Linux que utilizam **BTRFS + Timeshift**.

Ele automatiza todo o fluxo de manutenção do sistema:
- atualização de pacotes oficiais
- atualização de pacotes AUR
- atualização de Flatpaks
- limpeza de órfãos
- verificação de snapshots
- log de execução

---

## 💡 Por que este projeto existe?

Sempre gostei de realizar atualizações diretamente pelo terminal, principalmente no ecossistema Arch/BigLinux. Porém, antes de atualizar o sistema, eu acabava repetindo manualmente praticamente o mesmo ritual:

- verificar se existiam atualizações
- conferir snapshots do Timeshift
- procurar pacotes órfãos e remové-los
- atualizar repositórios oficiais
- atualizar AUR
- atualizar Flatpaks
- limpar dependências não utilizadas

Depois de fazer isso repetidamente por bastante tempo, resolvi automatizar todo o processo em um único comando.

O motivo real?

Preguiça.

Por isso fui montando esse script até ficar maduro o suficiente para compartilhar.

---
## ✨ Recursos Principais

### 🔒 Segurança Rigorosa (`Strict Mode`)

Utiliza:

```bash
set -euo pipefail
```

para interromper imediatamente a execução caso qualquer comando intermediário falhe, evitando atualizações parciais ou estados inconsistentes.

---

### 📝 Redirecionamento Inteligente de Logs

Cria automaticamente um log da execução do scritp em:

```bash
~/bigupdate.log
```

Registrando:

- atualizações pendentes
- pacotes atualizados
- horários de execução
- saída completa do sistema

Para eventual consulta caso falhe ou feche inesperadamente.

---

### 📸 Integração com Timeshift + BTRFS

Como o BigLinux trabalha com sistema de arquivos BTRFS por padrão e utiliza do Timeshift para criação de snapshots diárias, aproveitei no script para verificar automaticamente snapshots do dia atual antes da atualização e oferece criação automática de snapshot preventivo. 

Isso possibilita no BigLinux reverter todas as modificações do sistema escolhendo a snapshot anterior no menu de inicialização do sistema.

---

### 🧹 Gerenciamento de Pacotes Órfãos

Detecta e oferece remoção segura de dependências órfãs utilizando:

```bash
pacman -Qtdq
```

Ao longo do tempo, tive diversos problemas com pacotes órfãos sem manutenção ou suporte que acabavam interferindo em atualizações do sistema, especialmente no ecossistema Arch/Manjaro.

Como esses pacotes normalmente não são mais necessários, passei a removê-los antes das atualizações para reduzir a chance de conflitos, dependências quebradas ou falhas durante o processo de upgrade.

Por esse motivo, o script realiza a verificação antes da atualização do sistema e oferece a opção de limpeza segura dos pacotes órfãos encontrados.

Eu particularmente prefiro não remover órfãos imediatamente após as atualizações, já que algum software recém-atualizado ainda pode depender deles indiretamente. Como manter esses pacotes instalados temporariamente não costuma causar problemas, eles permanecem no sistema até a próxima execução do script.

---

### 🔄 Atualização Completa do Ecossistema

Atualiza:

- pacman
- yay/AUR
- Flatpak

---

### 🧼 Autolimpeza de Flatpaks

Remove runtimes e dependências Flatpak não utilizados após o update.

---

### 🔔 Notificações Desktop

Envia notificações via `notify-send` quando atualizações forem instaladas, recomendando reinicialização do sistema.

---

## 🛠️ Requisitos do Sistema

O script possui verificação automática de dependências.

Certifique-se de possuir:

- `yay`
- `flatpak`
- `timeshift`
- `libnotify`

Instalação recomendada:

```bash
sudo pacman -S flatpak timeshift libnotify
```

---

## 📦 Instalação

### 1. Clonar o repositório

```bash
git clone https://github.com/ReinaldoDiasAbreu/big-update.git
cd big-update
```

---

### 2. Permitir execução

```bash
chmod +x big-update
```

---

### 3. Executar

```bash
./big-update
```

---

## 🔧 Atualizando mirrors opcionalmente

O script possui suporte opcional para atualização automática da lista de mirrors utilizando:

```bash
./big-update --upmirrors
```

Essa opção executa:

```bash
sudo pacman-mirrors -f
```

antes da sincronização dos repositórios.

Embora útil em algumas situações, atualizar mirrors em toda execução normalmente é desnecessário e aumenta o tempo do processo de update. Na maioria dos casos, os mirrors já configurados continuam funcionando corretamente por longos períodos. Por esse motivo, no script deixei essa funcionalidade como opcional, permitindo atualizar os mirrors apenas quando necessário.

---

## 📄 Log

O log completo da execução é salvo em:

```bash
~/bigupdate.log
```

O arquivo é recriado automaticamente a cada execução para evitar crescimento excessivo. Apenas salva a saída do script com algumas informações a mais sobre os pacotes que foram atualizados e suas versões.

---

## 📜 Licença

MIT License

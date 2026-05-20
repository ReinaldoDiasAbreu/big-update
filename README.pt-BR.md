# 🚀 BigUpdate

<p align="center">
  <img src="https://img.shields.io/badge/OS-BigLinux%20%7C%20Arch%20%7C%20Manjaro-blue?style=for-the-badge&logo=linux" alt="OS Suportados">
  <img src="https://img.shields.io/badge/Shell-Bash%205+-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white" alt="Bash">
  <img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="MIT License">
</p>

<p align="center">
  <img src="./assets/demo.gif" alt="BigUpdate Demo">
</p>

O **BigUpdate** é um script de automação em Bash desenvolvido para sistemas **BigLinux Plasma* que utiliza **BTRFS + Timeshift**.

Ele automatiza todo o fluxo de manutenção do sistema:
- atualização de pacotes oficiais
- atualização de pacotes AUR
- atualização de Flatpaks
- limpeza de órfãos
- verificação de snapshots
- log de execução

---

## 💡 Por que este projeto existe?

Sempre gostei de realizar atualizações diretamente pelo terminal, principalmente no ecossistema Arch/Manjaro/BigLinux. Porém, antes de atualizar o sistema, acabava repetindo manualmente praticamente o mesmo ritual:

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

### 📝 Redirecionamento de Logs

Cria automaticamente um log simples da execução do scritp em:

```bash
~/bigupdate.log
```

Registrando:

- lista das atualizações pendentes
- pacotes atualizados
- horários de execução
- saída completa do sistema

Para eventual consulta caso falhe ou feche inesperadamente.

---

### 📸 Integração com Timeshift + BTRFS

Como o BigLinux padrão trabalha com sistema de arquivos BTRFS e utiliza do Timeshift para criação de snapshots diárias, aproveitei no script para verificar automaticamente snapshots do dia atual antes da atualização e oferece criação automática de snapshot preventivo. 

Isso possibilita no BigLinux que você possa reverter todas as modificações escolhendo a snapshot anterior no menu de inicialização.

---

### 🧹 Gerenciamento de Pacotes Órfãos

Detecta e oferece remoção segura de dependências órfãs utilizando:

```bash
pacman -Qtdq
```

Ao longo do tempo, tive diversos problemas com pacotes órfãos sem manutenção ou suporte que acabavam interferindo em atualizações do sistema.

Como esses pacotes **normalmente** não são mais necessários, passei a removê-los antes das atualizações para reduzir a chance de conflitos, dependências quebradas ou falhas durante o processo de upgrade.

Por esse motivo, o script realiza a verificação antes da atualização do sistema e oferece a opção de limpeza dos pacotes órfãos encontrados.

Eu particularmente, após atualizações, prefiro não remover órfãos imediatamente (o que também poderia ser feito após atualização), já que algum software ainda pode depender deles indiretamente (chance bem remota ao meu ver, dependendo do seu uso do sistema). Como manter esses pacotes instalados temporariamente não costuma causar problemas, eles permanecem no sistema até a próxima execução do script que fará a remoção antes de tentar atualizar novamente.

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

## 🛠️ Requisitos

O script possui verificação automática de dependências.

Certifique-se de possuir:

- `yay`
- `flatpak`
- `timeshift`
- `libnotify`

Instalação recomendada (não necessária no Big Linux Plasma):

```bash
sudo pacman -S flatpak timeshift libnotify
```

---

## 📦 Instalação

### 1. Clonar o repositório e entrar na pasta do projeto

```bash
git clone https://github.com/ReinaldoDiasAbreu/big-update.git
cd big-update
```

---

### 2. Instalação

```bash
makepkg -si
```

---

### 3. Executar

Após a conclusão da instalação, você pode iniciar o projeto de qualquer lugar do terminal rodando:

```bash
big-update
```
> [!NOTE]
>  Após o término da instalação, você pode apagar com segurança a pasta clonada (`big-update`). No entanto, **recomenda-se mantê-la**. Dessa forma, quando houver novas atualizações, bastará entrar na mesma pasta, rodar um `git pull` para baixar as novidades e executar o `makepkg -si` novamente para atualizar.

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


Exemplos de mensagens de error onde atualizar os mirrors é uma solução pois o sistema passa a utilizar servidores mais atualizados e estáveis.:

```text
error: failed retrieving file
error: database is not valid
error: failed to synchronize all databases
error: target not found
error: signature is invalid
error: corrupted database
```

Isso acontece porque os mirrors são réplicas distribuídas dos repositórios oficiais e nem sempre permanecem perfeitamente sincronizados entre si. Dependendo do estado do mirror selecionado, podem ocorrer situações como:

- o banco de dados já foi atualizado, mas o pacote ainda não foi replicado
- um mirror está temporariamente fora do ar
- o servidor possui alta latência
- arquivos foram parcialmente sincronizados
- algum mirror ficou desatualizado em relação aos demais
- houve falha de cache/CDN durante download

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

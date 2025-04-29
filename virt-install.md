# Instalando Debian Bookworm com virt-install no KVM/QEMU

Instalando o Debian Bookworm (Debian 12) diretamente da rede, utilizando virt-install com KVM/QEMU. O comando `virt-install` é uma ferramenta de linha de comando que facilita a criação de máquinas virtuais.

Primeiro, certifique-se de ter os pacotes necessários instalados:

```bash
sudo apt update
sudo apt install -y qemu-kvm libvirt-daemon-system virtinst bridge-utils
```

Agora, vamos criar uma máquina virtual com Debian Bookworm usando virt-install:

```bash
sudo virt-install \
  --name debian12 \
  --memory 2048 \
  --vcpus 2 \
  --disk size=20 \
  --os-variant debian12 \
  --network bridge=virbr0 \
  --graphics none \
  --console pty,target_type=serial \
  --location https://deb.debian.org/debian/dists/bookworm/main/installer-amd64/ \
  --extra-args "console=ttyS0,115200n8"
```

Explicação dos parâmetros:

- `--name debian12`: Define o nome da máquina virtual
- `--memory 2048`: Aloca 2GB de RAM
- `--vcpus 2`: Aloca 2 CPUs virtuais
- `--disk size=20`: Cria um disco virtual de 20GB
- `--os-variant debian12`: Especifica que o sistema operacional é Debian 12
- `--network bridge=virbr0`: Conecta a VM à rede padrão do libvirt
- `--graphics none`: Desabilita interface gráfica
- `--console pty,target_type=serial`: Configura o console serial
- `--location`: URL para o instalador do Debian Bookworm
- `--extra-args`: Parâmetros adicionais para o kernel, direcionando a saída para o console serial

Depois de executar este comando, você verá o instalador do Debian e poderá seguir as instruções na tela para completar a instalação.

Para gerenciar suas VMs posteriormente, você pode usar comandos como:

```bash
# Listar todas as VMs
virsh list --all

# Iniciar a VM
virsh start debian12

# Conectar ao console da VM
virsh console debian12

# Desligar a VM
virsh shutdown debian12
```

## Instalando Debian Bookworm com virt-install no KVM/QEMU usando ISO local

Este exemplo mostra como instalar o Debian Bookworm (Debian 12) usando virt-install com KVM/QEMU a partir de uma imagem ISO baixada localmente.

## Pré-requisitos

## Baixando a ISO do Debian Bookworm

Você pode baixar a ISO do Debian usando wget:

```bash
# Criar diretório para armazenar imagens ISO
mkdir -p ~/isos

# Baixar a ISO do Debian 12 (Bookworm)
wget -P ~/isos https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-12.5.0-amd64-netinst.iso
```

## Criando a máquina virtual com a ISO local

Use o comando `virt-install` para criar a VM a partir da ISO baixada:

```bash
sudo virt-install \
  --name debian12-local \
  --memory 2048 \
  --vcpus 2 \
  --disk size=20 \
  --os-variant debian12 \
  --network bridge=virbr0 \
  --cdrom ~/isos/debian-12.5.0-amd64-netinst.iso \
  --graphics vnc \
  --noautoconsole
```

### Explicação dos parâmetros:

- `--name debian12-local`: Define o nome da máquina virtual
- `--memory 2048`: Aloca 2GB de RAM
- `--vcpus 2`: Aloca 2 CPUs virtuais
- `--disk size=20`: Cria um disco virtual de 20GB
- `--os-variant debian12`: Especifica que o sistema operacional é Debian 12
- `--network bridge=virbr0`: Conecta a VM à rede padrão do libvirt
- `--cdrom`: Especifica o caminho para a ISO local que será usada como mídia de instalação
- `--graphics vnc`: Habilita acesso via VNC para a instalação gráfica
- `--noautoconsole`: Não conecta automaticamente ao console da VM

## Alternativa: Instalação no modo texto via console serial

Se preferir a instalação no modo texto via console serial, use este comando:

```bash
sudo virt-install \
  --name debian12-local-text \
  --memory 2048 \
  --vcpus 2 \
  --disk size=20 \
  --os-variant debian12 \
  --network bridge=virbr0 \
  --cdrom ~/isos/debian-12.5.0-amd64-netinst.iso \
  --graphics none \
  --console pty,target_type=serial \
  --extra-args "console=ttyS0,115200n8 serial"
```

## Conectando e gerenciando a VM

### Para VMs com interface gráfica (VNC)

Você pode usar o `virt-viewer` ou `virt-manager` para se conectar:

```bash
# Instalar virt-viewer se necessário
sudo apt install -y virt-viewer

# Conectar à VM
virt-viewer debian12-local
```

Alternativamente, inicie o Virtual Machine Manager:

```bash
sudo virt-manager
```

### Para VMs com console serial

```bash
# Conectar ao console da VM
virsh console debian12-local-text
```

## Comandos úteis para gerenciar suas VMs

```bash
# Listar todas as VMs
virsh list --all

# Iniciar a VM
virsh start debian12-local

# Desligar a VM
virsh shutdown debian12-local

# Forçar o desligamento (use apenas quando necessário)
virsh destroy debian12-local

# Remover uma VM e seu armazenamento
virsh undefine debian12-local --remove-all-storage
```

## Verificando informações da VM

```bash
# Ver informações detalhadas da VM
virsh dominfo debian12-local

# Ver dispositivos de disco da VM
virsh domblklist debian12-local

# Ver configuração de rede da VM
virsh domiflist debian12-local
```

Uma vez concluída a instalação, você terá um sistema Debian Bookworm funcional rodando como uma máquina virtual no KVM/QEMU. Você também pode usar ferramentas gráficas como o Virtual Machine Manager (`virt-manager`) se preferir uma interface gráfica para gerenciar suas VMs.

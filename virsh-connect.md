
### Especificando o servidor KVM a ser utilizado pelo virsh

As diferenças entre qemu:///system e qemu:///session ao usar virsh -c estão relacionadas ao nível de privilégio, local de execução e gestão de máquinas virtuais.

`qemu:///system` (Sistema Global)
Executa como root, pois gerencia VMs globalmente no sistema.

As VMs são armazenadas em `/var/lib/libvirt/qemu/` e `/var/lib/libvirt/images/`.

VMs iniciadas aqui podem ser acessadas por qualquer usuário com permissão.

Geralmente usado em servidores e ambientes de produção.

Mais seguro para administração centralizada.

`qemu:///session` (Sessão do Usuário)
Executa como usuário normal, sem precisar de permissões administrativas.

As VMs são armazenadas em `~/.local/share/libvirt/` e `~/.cache/qemu/`.

Cada usuário pode criar e gerenciar suas próprias VMs, sem interferir nas do sistema.

Não requer privilégios de root, tornando-o útil para testes e desenvolvimento.

Menos integração com o sistema, pois não afeta processos globais.

### Qual usar?

Para servidores e produção: qemu:///system (mais controle e persistência).

Para desenvolvimento e testes rápidos: qemu:///session (sem precisar de root).

### Verificar qual está ativo? Use virsh uri:

Para mudar o padrão do virsh para qemu:///system em vez de qemu:///session, você pode fazer o seguinte:

### Opção 1: Usar --connect no comando
Sempre que usar virsh, especifique manualmente:

```
virsh --connect qemu:///system
```
Isso força a conexão com o hypervisor do sistema.

### Opção 2: Exportar variável de ambiente
Se quiser que virsh sempre use qemu:///system, exporte a variável LIBVIRT_DEFAULT_URI:

```
export LIBVIRT_DEFAULT_URI=qemu:///system
```
Para tornar isso permanente, adicione ao ~/.bashrc ou ~/.bash_profile:

```
echo 'export LIBVIRT_DEFAULT_URI=qemu:///system' >> ~/.bashrc
source ~/.bashrc
```

### Opção 3: Alterar permissão de usuário
Se virsh insiste em usar qemu:///session, pode ser porque seu usuário não tem permissão para acessar o qemu:///system. 

Tente executar virsh como root:

```
sudo virsh
```
Se funcionar, você pode adicionar seu usuário ao grupo libvirt:

```
sudo usermod -aG libvirt $(whoami)
newgrp libvirt
```

A melhor opção é exportar LIBVIRT_DEFAULT_URI para garantir que virsh sempre use qemu:///system automaticamente.

### Passos para configurar a conexão remota

Defina a variável de ambiente para acessar um servidor remoto via SSH:

```
export LIBVIRT_DEFAULT_URI="qemu+ssh://usuario@servidor/system"
```
Substitua usuario pelo nome de usuário e servidor pelo endereço IP ou hostname do servidor remoto.

Torne a configuração permanente adicionando ao ~/.bashrc ou ~/.bash_profile:

```
echo 'export LIBVIRT_DEFAULT_URI="qemu+ssh://usuario@servidor/system"' >> ~/.bashrc
source ~/.bashrc
```

Verifique se a conexão funciona executando:

```
virsh uri
```

Se tudo estiver correto, a saída será:

`qemu+ssh://usuario@servidor/system`

### Dicas extras
Se precisar de autenticação sem senha, configure chaves SSH (ssh-keygen + ssh-copy-id).

Para usar um modo de sessão de usuário, altere system para session:

```
qemu+ssh://usuario@servidor/session
```

Para mais segurança, especifique uma porta SSH diferente, se necessário:

```
qemu+ssh://usuario@servidor:2222/system
```

Agora seu virsh se conectará automaticamente ao servidor remoto

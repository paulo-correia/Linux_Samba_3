# Samba 3

## Instalação
Toda a instalação é feita como **root**
### Debian
```apt-get install samba```
### CentOS
```yum install samba```
## Configurações Mínimas
Caso já exista um **/etc/samba/smb.conf** mover ele como **root** com o comando:

`mv /etc/samba/smb.conf /etc/samba/smb.ok`

Criar um arquivo **/etc/samba/smb.conf** com o seguinte conteúdo:
```
[global]
	workgroup = GRUPO
```
Onde o **GRUPO** é o nome do Compartilhamento / Grupo de Trabalho 

Testar as configurações com o comando:

`testparm`

Reiniciar o Samba com o comando:

`service smbd restart`
## Compartilhamentos
### Público

Criar abaixo do **[global]**:
``` 
[compartilhamento]
          path = \caminho\do\compartilhamento
          security = share
          read only = no
          guest ok = yes
          writable = yes
          guest only = yes
          guest account = nobody
          browsable = yes
```

Testar as configurações com o comando:

`testparm`

Reiniciar o Samba com o comando:

`service smbd restart`

### Home do Usuário (privado)

Criar abaixo do **[global]** e antes de qualquer compartilhamento:
```
[homes]
       comment = Home Directories
       browseable = no
       read only = yes
       writable = yes
       create mask = 0700
       directory mask = 0700
       valid users = %S
```

Testar as configurações com o comando:

`testparm`

Reiniciar o Samba com o comando:

`service smbd restart`

#### Observações
Os arquivos vão ficar na pasta /home/nome_do_usuario

### Compartilhamento Privado
Criar abaixo do **[global]**:
```
[compartilhamento]
	path = \caminho\do\compartilhamento
    writable = yes
```

Testar as configurações com o comando:

`testparm`

Reiniciar o Samba com o comando:

`service smbd restart`

## Criação de Usuários
Adicionar no Linux um usuário com o comando:
Para poder usar a Home do usuário:

`adduser usuario`

Para poder usar o Compartilhamento Privado:

`adduser --no-create-home --disabled-password --disabled-login usuario`

No Debian será solicitada a senha

No CentOS é preciso usar como **root** o comando **passwd usuario** e aí será solicitada a senha

Adicionar usuário no Samba com o comando:

`smbpasswd -a usuario`

Obs: A senha do samba pode ou **não** ser a mesma do usuário do Linux

## Comandos Úteis
Listar usuários do Samba (feito no servidor onde o samba está instalado)

`pdbedit -L -v`

Testar conexão em um compartilhamento (servidor ou estação) 

`smbclient //servidor/compartilhamento --user=usuário`

Listar compartilhamentos disponíveis para um usuário (servidor ou estação)

`smbclient -L //servidor --user=usuário`

Montar compatilhamento (feito na estação)

`mount -t cifs //servidor/compartilhamento /diretorio/pasta -o rw,noperm,uid=usuário,gid=grupo,username=usuário,password=senha_samba
`

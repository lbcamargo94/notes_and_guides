# Configurando acesso SSH em múltiplas contas do Github

### Comando para criar uma nova chave **SSH**

```sh
ssh-keygen -t ed25519 -C "seu_email@email.com"
```

- Em caso de sucesso, irá receber a seguinte mensagem no terminal, **"Generating public/private ed25519 key pair."**

---

### Na opção "Enter file in which to save the key(/default_local):"

- Digite o local desejado para para salvar a nova chave **SSH**.

**Exemplo:**

/c/users/my_username/.ssh/nome_da_key_ed25519  

- Em caso de sucesso, irá receber a seguinte mensagem, no terminal **"Created directory '/c/users/my_username/.ssh'."**

---

### Na opção "Enter passphrase (empty for no passphrase):"

- Digite a senha que desejar para a chave **SSH**.

- Se não desejar nenuma chave, basta somente apertar a tecla **Enter**.

- Se Você criou uma chave, lembre-se de usa-la sempre que acessar os comando do Git, com **git push** e **git pull**.

---

### Conferindo se a chave **SSH** foi criada no diretório

- Digite o comando no terminal.

```sh
ls .ssh/
```

- Deve ser retornado os nomes dos arquivos criados.

**Exemplo:**

1. nome_da_key_ed25519
2. nome_da_key_ed25519.pub

---

### Copiar conteudo da chave

- Digite o comando no terminal.

```sh
cat .ssh/nome_da_key_ed25519.pub
```

- Deve ser retornado no terminal o conteúdo da chave **SSH**.

- copie e cole em seu Git Hub.

---

### Configurar acesso **SSH** de multiplas contas Git

- Deve ser criado um arquivo de configurações no diretório ```~/.ssh/```.

- Digite o comando no terminal para criar no novo arquivo de configurações.

```sh
touch ~/.ssh/config
```

- Digite o comando no terminal para verificar se o arquivo foi criado com sucesso.

```sh
ls ~/.ssh/
```

- O arquivo deve ser editado e inserido as devidas configurações.

- Digite o comando no terminal para editaqr o arquivo de configurações.

```sh
vim ~/.ssh/config
```

- Configure o arquivo  relacionando qual conta Git vai acessar com qual chave SSH.

- inserir a linhas de configurações no arquivo de configurações.

```md
# Personal Account
Host github.com-nome_de_usuario
        HostName github.com
        User git
        IdentityFile ~/.ssh/nome_da_key_ed25519
```

- Salve o arquivo de configurações.

1. Precione a tecla **ESC**
2. Digite **:wq**
3. Precione a tecla **ENTER**

- Ao clonar um novo repositorio para a maquina local, deve ser alterado o host apenas.

**Exemplo Atual:**

```sh
git@github.com:usuariogithub/personal-repository.git
```

**Exemplo Modificado:**

```sh
git@github.com-nome_de_usuario:usuariogithub/personal-repository.git
```

- Para repositórios já existentes basta reconfigurar com o comando git remote.

- Digite o comando git remote -v, mais a origem do repositório.

```sh
git remote -v origin git@github.com-nome_de_usuario:usuariogithub/personal-repository.git

```

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

### Configurar acesso **SSH** de multiplas 
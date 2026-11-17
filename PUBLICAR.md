# Como publicar no seu GitHub

O repositório local já está pronto e com o primeiro commit feito:

```sh
cd /home/ext-wrezende/Documentos/deepseek-harness/default-workspace/dsh-excluir-sessao
```

O remoto já aponta para `https://github.com/wgrbiss-spec/dsh-excluir-sessao.git`.

## 1. Crie o repositório vazio no GitHub

Abra <https://github.com/new> e crie um repositório chamado **dsh-excluir-sessao**.
Não marque "Add a README", "Add .gitignore" nem escolha licença: o repositório precisa nascer vazio.

## 2. Envie o código

No seu terminal (o `git` vai pedir usuário e um token pessoal do GitHub no lugar da senha):

```sh
cd /home/ext-wrezende/Documentos/deepseek-harness/default-workspace/dsh-excluir-sessao
git push -u origin main
```

## 3. Instale no DeepSeek Harness

Antes, remova o plugin original para não ficarem dois menus "Excluir":

```sh
dsh plugin --profile desktop remove dsh-delete-session
dsh plugin --profile desktop add github:wgrbiss-spec/dsh-excluir-sessao
```

Reinicie o aplicativo. A interface passa a aparecer em português quando o navegador está em pt-BR.

## Alternativa sem GitHub: instalar pelo pacote local

O arquivo `dsh-excluir-sessao-0.1.0.tgz` (gerado com `npm pack`) pode ser instalado direto:

```sh
dsh plugin --profile desktop add /home/ext-wrezende/Documentos/deepseek-harness/default-workspace/dsh-excluir-sessao/dsh-excluir-sessao-0.1.0.tgz
```

## Publicar no npm (opcional)

```sh
npm publish
```

Depois disso também funciona `dsh plugin --profile desktop add dsh-excluir-sessao`.

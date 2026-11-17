# Instalação e publicação

O plugin já está publicado no GitHub:

- Repositório: <https://github.com/wgrbiss-spec/dsh-excluir-sessao>
- Versão: `v0.1.0`

## 1. Instale no DeepSeek Harness

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

Se precisar regerar o pacote depois de mudanças:

```sh
npm pack
```

## Publicar uma nova versão

1. Atualize `version` no `package.json` e o `CHANGELOG` (se houver)
2. `git commit -am "chore: versão X.Y.Z" && git tag vX.Y.Z && git push --follow-tags`
3. Crie o release: `gh release create vX.Y.Z` ou pela página *Releases* do GitHub

## Publicar no npm (opcional)

```sh
npm publish
```

Depois disso também funciona `dsh plugin --profile desktop add dsh-excluir-sessao`.

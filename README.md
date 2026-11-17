# dsh-excluir-sessao

**Plugin de exclusão de sessões para o DeepSeek Harness — apague uma sessão direto no menu da barra lateral.**

Adiciona **Excluir sessão** ao menu `...` da sessão na barra lateral do DSH Web (o mesmo menu de Renomear / Derivar / Arquivar) e, opcionalmente, recolhe os arquivos que aquela conversa produziu.
A exclusão vai para a **lixeira do sistema** por padrão, então um clique errado pode ser restaurado; para apagar de vez, basta desligar essa opção nas configurações.

> Esta é a versão em **português do Brasil** do plugin [dsh-delete-session](https://github.com/DDDMUC/dsh-delete-session). Toda a interface, as mensagens de erro e esta documentação foram traduzidas para pt-BR; os idiomas originalmente suportados (chinês, inglês e russo) continuam disponíveis.

[![npm](https://img.shields.io/npm/v/dsh-excluir-sessao)](https://www.npmjs.com/package/dsh-excluir-sessao)
[![license](https://img.shields.io/npm/l/dsh-excluir-sessao)](./LICENSE)

## Por que ele existe

O menu de sessão da barra lateral do DSH só oferece **Renomear / Derivar / Arquivar** — arquivar apenas esconde a sessão, **não apaga os dados**; e o gerenciador de arquivamento fica no fundo das configurações, exigindo várias telas para excluir uma única sessão.

Este plugin devolve o **Excluir** para o lugar onde você mais clica: o menu `...` da linha da sessão.

## Recursos

- **Excluir pelo menu** — registrado no slot oficial do host `sidebar.workspaces.session.menu.item`, logo abaixo de "Arquivar sessão"; o ID da sessão é injetado pelo host, então a ação sempre atinge exatamente a linha clicada e **nunca troca a sessão atual**
- **Excluir para a lixeira do sistema (ligado por padrão)** — o log da sessão, a contabilização do workspace e o cache de projeção vão juntos para a **lixeira do sistema**: Lixeira do Windows, Lixeira do macOS, `gio trash` no Linux. Errou? Restaure pela lixeira. A página de configurações permite desligar isso; desligado, a remoção é **direto do disco, sem volta**
- **Texto do diálogo acompanha a opção** — marcada, a frase fala em "enviar para a lixeira do sistema"; desmarcada, fala em "excluir permanentemente… não pode ser desfeito". O que você lê é exatamente o que vai acontecer
- **Confirmação de risco** — cada exclusão abre um diálogo com o título e o ID da sessão. Se o diálogo aparece ou não é decidido **apenas na página de configurações**, sem atalho dentro do pop-up; **segurar Shift ao clicar** força o diálogo temporariamente
- **Excluir as subsessões junto** — subagentes são **sessões independentes** (diretório e log próprios) e viram órfãos no disco quando a sessão-mãe some. A exclusão agora trata as subsessões derivadas: marcando "Excluir também as subsessões" nas configurações, tudo vai junto; desmarcado, o diálogo lista as subsessões e você escolhe quais excluir (todas marcadas por padrão)
- **Exclusão em lote por seleção múltipla** — o item "Selecionar várias" do menu entra no modo de seleção: marque várias linhas e a barra de ações aparece no rodapé da barra lateral (slot oficial `sidebar.footer.action`), com "N selecionadas / Cancelar / Excluir"; a posição é organizada pelo host e sai ao sair do modo. A exclusão roda em lote, as linhas somem de forma otimista e a barra mostra o progresso em tempo real; o cliente faz 6 requisições em paralelo e a contabilização do workspace é serializada; linhas com falha voltam a aparecer e continuam marcadas para nova tentativa; Esc sai do modo
- **Quarentena de arquivos produzidos (opcional, desligada por padrão)** — quando ligada, excluir uma sessão também **move para uma pasta de quarentena** os arquivos que aquela conversa produziu no diretório de trabalho, em vez de apagá-los; o escopo pode ser "apenas criados" ou "criados e alterados", e o modo pode ser "tudo direto para a quarentena" ou "listar primeiro para você marcar"
- **Ignorar arquivos dentro de repositórios git (ligado por padrão)** — conversas que trabalharam num projeto costumam ter o código-fonte entre os arquivos produzidos. Com a opção ligada, nada dentro do repositório é tocado, evitando que excluir uma conversa esvazie o projeto
- **Voltar para uma sessão nova após excluir** — se a sessão excluída for a que está aberta, o DSH muda para uma sessão nova em vez de cair numa vizinha já inutilizável
- **Seguro por padrão** — as rotas do host aceitam apenas conexões locais (socket de loopback + cabeçalho Host de loopback + verificação de mesma origem); sessões ainda abertas no DSH são recusadas com 409, nunca competindo com quem está gravando
- **Quatro idiomas** — menu, diálogos e página de configurações seguem o idioma do DSH (zh / en); em navegador **pt-BR** a interface aparece em português e em russo aparece em russo (o DSH ainda não oferece esses idiomas, então o próprio plugin traz os dicionários). Esta é a diferença desta versão em relação ao plugin original
- **Zero dependências** — não importa nenhum SDK do DSH; todos os serviços (`sessions` / `sessionPersistence` / `agents` / `workspaceRegistry` / `webServer`) são obtidos do contexto cordis no momento da chamada

## Instalação

Pelo npm:

```sh
dsh plugin --profile web add dsh-excluir-sessao
```

Ou diretamente do GitHub:

```sh
dsh plugin --profile web add github:wgrbiss-spec/dsh-excluir-sessao
```

Reinicie o `dsh web` para ativar.

No aplicativo DSH Desktop, abra **Configurações → Plugins** e instale por lá. O app gerencia o próprio perfil; instalar pela CLI em `profiles/web` não alcança o aplicativo desktop.

## Como usar

1. Passe o mouse sobre a linha da sessão na barra lateral e clique em `...`
2. Escolha **Excluir sessão**
3. Na primeira vez aparece uma confirmação; para não vê-la mais, desligue **Perguntar antes de excluir** na página do plugin
4. Errou? Abra a lixeira do sistema, encontre `session-<id>` e `session-<id>.json` e restaure
5. Em lote: menu da sessão → **Selecionar várias** → marque várias → **Excluir** no rodapé; Esc ou **Cancelar** sai do modo

## Configurações

Página do plugin → **dsh-excluir-sessao**:

| Configuração | Padrão | O que faz |
| --- | --- | --- |
| Perguntar antes de excluir | Ligado | Desligado, o item do menu exclui na hora, sem diálogo (mas com arquivos produzidos em "listar primeiro" o diálogo ainda abre, pois a lista só existe nele) |
| **Excluir para a lixeira** | **Ligado** | Ligado: vai para a lixeira do sistema e pode ser restaurado. Desligado: remove direto do disco, sem restauração |
| Não tratar arquivos produzidos | Ligado | Marcado, os arquivos produzidos **não** são tratados — comportamento igual ao das versões antigas |
| Enviar arquivos produzidos direto para a quarentena | Desligado | Desligado: lista primeiro e você marca. Ligado: move tudo em bloco, sem listar nem verificar |
| Escopo: apenas criados / criados e alterados | Apenas criados | Mexe só nos arquivos criados pela conversa ou também nos arquivos já existentes que ela reescreveu |
| **Ignorar arquivos dentro de repositórios git** | **Ligado** | Nada dentro do repositório é tocado |
| **Excluir também as subsessões** | **Desligado** | Ligado: exclui junto as sessões de subagentes derivadas. Desligado: o diálogo lista as subsessões para você marcar |
| Local da pasta de quarentena | Vazio (= `$DSH_HOME`) | Pode digitar, usar **Escolher pasta…** (diálogo do sistema) ou **Abrir** para localizar no gerenciador de arquivos |

Cada linha da lista de arquivos produzidos tem dois botões à direita: **Abrir pasta de destino** (localiza no gerenciador de arquivos) e **Excluir este arquivo** (o destino segue a opção "Excluir para a lixeira": ligada vai para a lixeira, desligada apaga direto). A exclusão só vale para caminhos listados na varredura atual; qualquer outro caminho enviado na requisição é recusado pelo host.

> **Escolher pasta…** usa o `ctx.directoryPicker` do próprio host: em desktop local abre o diálogo do sistema; em hosts remotos ou no modo navegador cai automaticamente na lista de diretórios da página. O plugin não traz mais nenhum seletor desenhado ou compilado por ele mesmo.

## Para onde vão os arquivos depois da exclusão

| Plataforma | Destino |
| --- | --- |
| Windows | Lixeira do sistema (`SendToRecycleBin` de `Microsoft.VisualBasic.FileIO.FileSystem`) |
| macOS | Lixeira (Finder, via `osascript`) |
| Linux | Lixeira da área de trabalho (`gio trash`) |
| Quando nada disso está disponível | `$DSH_HOME/session-trash-<datahora>-<pid>-<sequência>/`, para mover de volta manualmente |

A ordem é **primeiro renomear para uma área de preparo e depois enviar para a lixeira em segundo plano**: renomear leva microssegundos, enquanto a operação da lixeira do sistema leva ~600 ms, então a interface não espera. Se o envio em segundo plano falhar, os arquivos continuam na área de preparo e não se perdem.

Com **Excluir para a lixeira** desmarcado, o diretório é removido com `rm`, sem cópia na área de preparo e sem nada na lixeira.

## Quarentena de arquivos produzidos

Este recurso vem **desligado por padrão**. Quando ligado (página do plugin → dsh-excluir-sessao), excluir uma sessão também trata os **arquivos que aquela conversa tocou no diretório de trabalho**:

| Escopo | O que é tratado |
| --- | --- |
| Apenas arquivos criados (padrão) | Arquivos criados pela própria conversa |
| Arquivos criados e alterados | Inclui também arquivos **já existentes** que a conversa reescreveu — movê-los altera o que já estava no diretório de trabalho |

- **Move, não apaga**: os arquivos vão para `$DSH_HOME/purge-quarantine-<datahora>/`, preservando o caminho relativo ao diretório de trabalho. Cada exclusão **ocupa** um novo diretório de quarentena (se o nome já existir, ele é ajustado) e **nunca sobrescreve** um arquivo existente
- **Calcula antes de agir**: antes de excluir, o host deriva a lista novamente a partir do log da sessão; o que você marca na interface só pode reduzir o escopo, nunca aumentar
- **Como "criado" é decidido**: pela invariante do `fs-observation-policy` — antes de sobrescrever um arquivo existente com `write`, é preciso ter lido com sucesso com `read`; logo, "não houve leitura bem-sucedida antes da primeira escrita" significa criado. A decisão é por **identidade do arquivo**, não pela grafia do caminho: apelidos de symlink e diferenças apenas de maiúsculas/minúsculas no Windows contam como o mesmo arquivo
- **Quatro filtros de segurança**: precisa ser um arquivo comum, o caminho real precisa estar estritamente dentro do diretório de trabalho, fora do `$DSH_HOME`, e o arquivo precisa ainda existir; o que for ignorado é listado no diálogo com o motivo
- **Exclusão em lote não usa este recurso**: só a exclusão individual trata arquivos produzidos

## Integração com o dsh-session-relocate (opcional)

Com o [dsh-session-relocate](https://github.com/DDDMUC/dsh-session-relocate) instalado, a barra da seleção múltipla ganha um botão **Mover**, que transfere as sessões selecionadas em lote para outro workspace.

Os dois conversam por um conjunto de capacidades públicas; faltando um dos lados, você só perde um botão, nunca a exclusão:

- Este plugin, em `apply()`, define `window.__DSH_EXCLUIR_SESSAO__ = { supportsBulkMove: true }` e dispara o evento `dsh-excluir-sessao:ready`
- O relocate, ao ver isso, esconde o próprio conjunto de entrada em lote e expõe `window.__DSH_SESSION_RELOCATE__.moveSelection(ids, { onProgress })`
- O retorno é sempre `{ canceled?, moved, failed, skipped, total, results }`; campo faltando vira erro explícito, sem suposições de reserva

## Desempenho

| Operação | Tempo |
| --- | --- |
| Excluir (com lixeira) | ~60 ms para responder; a lixeira termina em segundo plano |
| Excluir (sem lixeira) | ~10 ms |
| Abrir o diálogo de pastas do sistema | ~120 ms (helper compilado); a primeira compilação roda em segundo plano na carga do plugin |
| Varredura de arquivos produzidos | ~20 ms numa conversa longa com 1000 chamadas de ferramenta |

Os dois helpers (seleção de diretório e envio para a lixeira) são compilados com `csc.exe` em um pequeno exe no primeiro uso e ficam em cache em `$DSH_HOME/cache/dsh-excluir-sessao/`; o plugin já os compila em segundo plano ao carregar, então o primeiro clique não espera. Máquinas sem `csc` caem automaticamente no caminho via PowerShell, apenas mais lento.

## Testes

```sh
node test/artifacts.host.test.js   # 59 testes, metade do host
node test/checkbox.dom.test.js     # 67 testes, metade do cliente
```

Os testes do cliente executam o `src/client.js` real sobre uma implementação de DOM legível, acionando os **componentes registrados nos slots**, a árvore de elementos que eles renderizam e o DOM que o próprio plugin cria. Os testes do host executam o `src/index.js` real e cobrem a decisão "criado vs. alterado", cada filtro de segurança, a movimentação para quarentena preservando a estrutura de diretórios, o salto de repositórios git e os caminhos de lixeira e exclusão direta.

Ambos os conjuntos passaram por **validação por mutação**: ao reverter a implementação para o comportamento antigo ou quebrá-la de propósito, as asserções correspondentes precisam falhar. Isso já pegou vários pontos que "pareciam testados, mas não estavam" (por exemplo, campos na requisição de varredura e os ramos de opção no host).

## Limitações conhecidas

- **No macOS, o primeiro uso pede permissão de automação do Finder**; se recusada, os arquivos ficam no diretório de preparo — o plugin não finge que apagou
- Sessões abertas no DSH: a exclusão espera a liberação da propriedade de escrita (normalmente alguns segundos) e conclui; em casos raros de sessão ainda ocupada, o plugin pede para tentar de novo depois ou reiniciar o DSH
- As caixas de seleção múltipla ainda são uma decoração de DOM sobre a linha da sessão, dependendo do atributo `data-row-key="session:<id>"` do host; se a interface principal renomear isso, a seleção múltipla falha silenciosamente (**o menu e os diálogos não são afetados**, pois usam os slots oficiais)
- A limpeza de arquivos produzidos vem desligada e tem algumas bordas mesmo ligada: arquivos criados via `bash` / `pwsh` (`npm run build`, `git clone`, `> arquivo`) **nunca são detectados**; **diretórios nunca são movidos**; a lista tem limite de 500 itens; **não há interface de restauração** — a quarentena precisa ser movida de volta manualmente
- A decisão "criado vs. alterado" depende de o `fs-observation-policy` continuar ativo
- A exclusão em lote (seleção múltipla) **não** trata arquivos produzidos nem subsessões
- As pastas de preparo e quarentena **não têm limpeza automática** e se acumulam em `$DSH_HOME`

## Compatibilidade

- **Metade do host** testada no DSH `0.2.1-rc.2`: socket de loopback, cabeçalho Host, proteção de origem cruzada, validação do ID de sessão, rotas 405/404
- **Metade do cliente** testada no DSH `0.2.1-rc.2`; a versão após a migração de slots também foi executada no navegador
- Em versões antigas sem os serviços necessários, o fluxo do host degrada para limpeza puramente no sistema de arquivos

Se este plugin te ajudou, deixe uma ⭐ no repositório ([GitHub](https://github.com/wgrbiss-spec/dsh-excluir-sessao)).

## Licença e créditos

MIT — © 2026 DDDMUC, autor do [dsh-delete-session](https://github.com/DDDMUC/dsh-delete-session) original.
Esta é uma tradução para português do Brasil mantida por [wgrbiss-spec](https://github.com/wgrbiss-spec), publicada sob a mesma licença MIT. O código-fonte, a arquitetura e os textos originais em chinês, inglês e russo pertencem ao projeto original.

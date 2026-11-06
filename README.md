# SGQ Editor de Fluxogramas

Editor de fluxogramas de processos do Sistema de Gestão da Qualidade (SGQ). Abre os fluxogramas que estão dentro de documentos do Word (inclusive os feitos no SmartDraw), permite editar tudo de forma visual e grava o resultado de volta no mesmo documento.

![Editor de fluxogramas V3](docs/editor.png)

**Versão atual: 3.0**

## Download

Baixe o arquivo `SGQ_Editor_Fluxogramas_V3.zip` na página de [Releases](../../releases). Ele contém o executável e o LEIA-ME.

Para ver a interface sem instalar nada, abra o `index.html` no navegador ou pelo GitHub Pages. Nesse modo, as funções que dependem do Word ficam desativadas.

## Recursos

**Leitura e gravação no Word**
- Abre arquivos `.doc` e `.docx` e lista todos os fluxogramas do documento.
- Lê os fluxogramas do SmartDraw de forma nativa e usa OCR quando necessário.
- Grava as alterações no próprio documento. A imagem salva é recortada sem margem e limitada a 3200 px.

**Edição**
- Formas: Início/Fim, Processo, Decisão, Texto e colunas de responsáveis.
- Ações: editar, duplicar, excluir, desfazer, refazer e auto-ligar novas caixas.
- Trocar o tipo da forma, alinhar várias formas e redimensionar pelos quatro cantos.

**Linhas livres**
- Selecione uma seta e arraste as bolinhas brancas para mover cada trecho.
- Arraste as pontas verdes para escolher o ponto de saída ou de chegada na caixa.
- A rota ajustada à mão é mantida quando você move as caixas.
- Setas que saem do mesmo lado de uma caixa saem separadas. No losango, partem do mesmo vértice e se dividem logo depois.
- Os rótulos SIM e NÃO ficam como texto limpo ao lado da linha. A caixinha é opcional.

**Cores**
- Preenchimento, borda, cor do texto, cor da seta, espessura e linha tracejada.
- Temas para o fluxo inteiro: Padrão SGQ, Preto e branco, Verde-água e Azul institucional.

**Área de trabalho no estilo CAD**
- Réguas, grade com encaixe e coordenadas X e Y no rodapé.
- Campos de posição e tamanho exatos no painel de propriedades.
- Zoom com Ctrl + roda do mouse e botão de ajustar à tela.

**Exportação**
- PNG, JPG, SVG, arquivo do fluxo (`.json`), impressão ou PDF, e Word.

**Manutenção dentro do app**
- *Limpar cache e reiniciar*: fecha o Word oculto, apaga sessões temporárias e reinicia o motor.
- *Fechar Word oculto*: encerra só as instâncias do Word sem janela.
- O motor se desliga sozinho alguns minutos depois que a janela é fechada.

![Ajuste de rota de uma seta](docs/linhas.png)

## Instalação

1. Extraia o zip. Use botão direito e "Extrair tudo"; não abra o executável de dentro do zip.
2. Dê dois cliques em `SGQ_Editor_Fluxogramas_V3.exe`.

Na primeira execução, o aplicativo encerra versões antigas que ficaram abertas em segundo plano, remove a instalação anterior em `%LOCALAPPDATA%\SGQ_Fluxogramas` e abre a V3. Não é necessário rodar nenhum `.bat`.

Se o Windows exibir "O Windows protegeu o computador", clique em **Mais informações** e depois em **Executar assim mesmo**.

### Requisitos

- Windows 10 ou 11, 64 bits.
- Microsoft Word instalado.
- Microsoft Edge ou Google Chrome.
- Não precisa de internet: o Node.js oficial (v22.16.0, assinado pela OpenJS Foundation) já vem dentro do executável.

## Atalhos

| Tecla | Ação |
|---|---|
| `V` | Selecionar |
| `C` | Conectar |
| `F2` | Editar texto |
| `Delete` | Excluir |
| `Ctrl+D` | Duplicar |
| `Ctrl+Z` / `Ctrl+Y` | Desfazer / Refazer |
| Setas (`Shift` para passo maior) | Mover a seleção |
| `Ctrl` + roda do mouse | Zoom |
| Duplo clique | Editar texto da forma ou rótulo da seta |

## Como funciona

```
SGQ_Editor_Fluxogramas_V3.exe   (inicializador em Go)
 ├─ instala em %LOCALAPPDATA%\SGQ_Fluxogramas\app
 ├─ extrai o Node.js embutido em ...\node
 ├─ inicia o motor:  node server.js  (http://127.0.0.1:8767)
 │    └─ scripts PowerShell automatizam o Word (ler, renderizar, gravar)
 └─ abre o editor em janela própria (Edge/Chrome em modo aplicativo)
```

| Arquivo | Função |
|---|---|
| `app/editor_fluxograma.html` | Editor (interface, desenho, rotas, exportação) |
| `app/server.js` | Motor local: sessões, comunicação com o Word, manutenção |
| `app/inspect_word.ps1` | Lista os fluxogramas de um documento |
| `app/render_flow.ps1` | Renderiza um fluxograma do Word para importação |
| `app/save_word_flow.ps1` | Grava o fluxograma editado de volta no documento |
| `app/sdr_geometry.js`, `app/sdr_diagram.js` | Leitura nativa do formato SmartDraw |
| `launcher/main.go` | Inicializador do Windows |
| `index.html` | Cópia do editor para abrir no navegador ou no GitHub Pages |

Os campos novos dos conectores (`fromSide`, `toSide`, `fromT`, `toT`, `manual`, `dash`) são opcionais. Arquivos `.json` e documentos salvos nas versões 2.x abrem normalmente.

## Compilar a partir do código

Requisitos: Go 1.22 ou superior e npm. O `osslsigncode` é opcional e serve para conferir a assinatura do Node.js.

```bash
./build.sh
# resultado: dist/SGQ_Editor_Fluxogramas_V3.exe
```

O script copia `app/` para o inicializador e obtém o `node.exe` oficial 22.16.0 pelo pacote `node-win-x64`. Em seguida, confere o SHA-256 e a assinatura, embute o ícone e compila para Windows x64. O executável gerado não depende de internet.

## Solução de problemas

| Situação | O que fazer |
|---|---|
| Fluxo travado ou Word preso | Use **Manutenção > Limpar cache e reiniciar** |
| Antivírus bloqueou o `node.exe` | Peça à TI para liberar `%LOCALAPPDATA%\SGQ_Fluxogramas\node` |
| Motor não inicia | Envie o arquivo `%LOCALAPPDATA%\SGQ_Fluxogramas\motor.log` |
| Mensagem de erro do Word | Tire um print da mensagem inteira; ela traz o diagnóstico `[diag: ...]` |

## Histórico de versões

**3.0**
- Nova interface, mais leve, com painel de propriedades, réguas e coordenadas.
- Linhas ajustáveis trecho a trecho, com escolha do ponto de saída e de chegada.
- Cores, espessuras, linha tracejada e temas de cor.
- Rótulos SIM e NÃO como texto limpo.
- Limpeza de cache dentro do app, com detecção e remoção automática de versões antigas.
- Node.js embutido: não precisa de internet.

**2.5**
- Motor V51: imagem limitada a 3200 px, recorte sem margem e diagnóstico nas mensagens de erro.

## Licença

Defina a licença deste repositório antes de publicá-lo. O Node.js embutido no executável é distribuído sob a [licença do Node.js](https://github.com/nodejs/node/blob/main/LICENSE).

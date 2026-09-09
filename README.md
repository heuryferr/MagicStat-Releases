# MagicStat-Releases — Distribuição oficial do Magic Stat

> **Este repositório é PÚBLICO de propósito, mas NÃO contém código-fonte.**
> Existe para uma única função: **servir os instaladores** (`.pkg` / `.exe` /
> `.AppImage`) e o **manifesto de atualização** que o app consulta.
>
> O código-fonte vive 🔒 **privado** em `heuryferr/Magic-Stat` — nunca vai
> aparecer aqui.

| O que | Onde |
|---|---|
| Instaladores (download direto) | **Releases** deste repositório (aba *Releases*) |
| Manifesto de atualização (o app lê) | `updates/manifest.json` |
| Código-fonte | 🔒 privado (`heuryferr/Magic-Stat`) |

---

## 1. Os 3 instaladores (um por sistema operacional)

O Magic Stat roda em **macOS, Windows e Linux** — e cada sistema usa um
formato de instalador e um jeito diferente de se atualizar:

| SO | Arquivo que você gera | 1ª instalação | **Atualização automática** (o app faz sozinho) |
|---|---|---|---|
| **macOS** | `MagicStat-<v>.pkg` | instala em /Applications | baixa → confere hash → pede a **senha de admin** (janela do macOS) → instala → **relança sozinho** na versão nova |
| **Windows** | `MagicStat-<v>-setup.exe` | instalador padrão | baixa → confere hash → **abre o instalador** (UAC) → usuário clica em Instalar |
| **Linux** | `MagicStat-<v>.AppImage` | arquivo único (2 cliques) | baixa → confere hash → **substitui o arquivo** (sem senha) → relança |

> Pode publicar **só um** deles (ex.: só macOS) — o app de cada plataforma
> procura **o instalador da própria plataforma** no manifesto e ignora os
> outros. O manifesto aceita 1, 2 ou 3 blocos.

---

## 2. Links oficiais (padrão SEMPRE igual)

```
Download direto de uma versão:
  https://github.com/heuryferr/MagicStat-Releases/releases/download/v1.1.0/MagicStat-1.1.0.pkg
  https://github.com/heuryferr/MagicStat-Releases/releases/download/v1.1.0/MagicStat-1.1.0-setup.exe
  https://github.com/heuryferr/MagicStat-Releases/releases/download/v1.1.0/MagicStat-1.1.0.AppImage

Manifesto que o app consulta (raw):
  https://raw.githubusercontent.com/heuryferr/MagicStat-Releases/main/updates/manifest.json
```

**O site (Gumroad/landing) nunca hospeda o arquivo** — ele só aponta o botão
de download para a URL acima. Assim o GitHub conta os downloads de graça e a
mesma URL serve o download inicial e a atualização.

---

## 3. Como publicar uma versão nova — passo a passo (dono)

> ⏱️ Depois da 1ª vez: ~5 minutos. Preparação (uma vez só): o `gh` instalado e
> autenticado, e um **clone deste repositório** na sua máquina:
>
>     gh repo clone heuryferr/MagicStat-Releases ~/MagicStat-Releases

### Passo 1 — Gere e NOMEIE os instaladores

Gere os instaladores com a versão no nome (obrigatório para o mecanismo):

```
MagicStat-1.1.0.pkg          ← macOS  (ver GERAR_PKG_MACOS.md no repo do código)
MagicStat-1.1.0-setup.exe    ← Windows
MagicStat-1.1.0.AppImage     ← Linux
```

### Passo 2 — Rode a ferramenta (no repo do CÓDIGO)

Ela calcula o **SHA-256** e o **tamanho** de cada arquivo, monta a URL de cada
um e reescreve o `updates/manifest.json`:

```bash
cd ~/Da\ nuvem\ magic/Magic-Stat        # repo do CÓDIGO (privado)

~/meu_ambiente/bin/python tools/publicar_update.py 1.1.0 \
    --pkg ~/Desktop/MagicStat-1.1.0.pkg \
    --exe ~/Desktop/MagicStat-1.1.0-setup.exe \
    --appimage ~/Desktop/MagicStat-1.1.0.AppImage \
    --dist ~/MagicStat-Releases
```

O que acontece:
- imprime o SHA-256 + tamanho + URL de cada um dos 3;
- escreve `updates/manifest.json` no repo do código **e** (por causa do
  `--dist`) **copia e faz commit+push no repo público de distribuição** —
  o manifesto já fica no ar;
- imprime o comando `gh release create` pronto (Passo 3).

> Só macOS por enquanto? `--pkg` basta (o manifesto fica com 1 bloco).

### Passo 3 — Suba os 3 arquivos na Release

Rode o comando que a ferramenta imprimiu (exemplo):

```bash
gh release create v1.1.0 \
    MagicStat-1.1.0.pkg \
    MagicStat-1.1.0-setup.exe \
    MagicStat-1.1.0.AppImage \
    --repo heuryferr/MagicStat-Releases \
    --title "Magic Stat 1.1.0" \
    --notes "O que há de novo nesta versão (para humanos)."
```

> O `v` na tag é obrigatório (o app monta a URL com `v<versão>`).
> Sem o `gh`, dá para fazer pela página do GitHub → aba **Releases** → *Draft
> a new release* → arrasta os 3 arquivos.

### Passo 4 — Confira (30 segundos, opcional)

```bash
curl -s https://raw.githubusercontent.com/heuryferr/MagicStat-Releases/main/updates/manifest.json
# deve mostrar "latest_version": "1.1.0" e os 3 blocos com url/sha256 preenchidos

curl -sI https://github.com/heuryferr/MagicStat-Releases/releases/download/v1.1.0/MagicStat-1.1.0.pkg
# deve responder HTTP 200/302 (o arquivo existe)
```

### Passo 5 — Pronto

Cada usuário com uma versão **anterior** e licença válida vê o aviso na
próxima checagem automática (+12 s ao abrir o app, ou **Help → Check for
Updates**). A mensagem que ele vê vem do `display` do manifesto (marketing —
nunca "bug corrigido").

---

## 4. Teste do mecanismo sem a versão definitiva

Use o MESMO arquivo para download inicial e para o "update de teste":
rode a ferramenta com `--qa-latest` para gerar um manifesto fictício com
versão maior apontando para o mesmo instalador (detalhes no `DISTRIBUICAO.md`
do repo do código, seção 3.3).

---

## 5. Regras

- **Nunca** suba código-fonte, dados de clientes ou chaves aqui.
- `manifest.json` = o "índice" do app (versão + link + hash + mensagem).
- Publique o manifesto com `--dist` **depois** de a Release existir (o hash
  só confere se o arquivo já estiver no ar — mas o app só avisa quando as
  DUAS coisas estão prontas).
- Repositórios: código 🔒 privado (`Magic-Stat`) · distribuição 🌐 público
  (`MagicStat-Releases`).

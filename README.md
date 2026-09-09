# MagicStat-Releases — Distribuição oficial do Magic Stat

> **Este repositório é PÚBLICO de propósito, mas NÃO contém código-fonte.**
> Ele existe para uma única função: **servir os instaladores** (`.pkg` /
> `.exe` / `.AppImage`) e o **manifesto de atualização** que o app consulta.

| O que | Onde |
|---|---|
| Instaladores (download direto) | **Releases** deste repositório → aba *Releases* |
| Manifesto de atualização (o app lê) | `updates/manifest.json` |
| Código-fonte | 🔒 **privado** (`heuryferr/Magic-Stat`) — nunca publicado aqui |

## Links oficiais (padrão)

```
Download direto de uma versão:
  https://github.com/heuryferr/MagicStat-Releases/releases/download/v1.0.1/MagicStat-1.0.1.pkg

Manifesto que o app consulta (raw):
  https://raw.githubusercontent.com/heuryferr/MagicStat-Releases/main/updates/manifest.json
```

## Como publicar uma versão (dono)

> Guia completo (incluindo teste do mecanismo): `DISTRIBUICAO.md` no repo do
> código. Resumo:

```bash
# 1) no repo do CÓDIGO, rode a ferramenta (calcula sha256, monta o manifesto,
#    imprime o comando de release):
~/meu_ambiente/bin/python tools/publicar_update.py 1.0.1 --pkg MagicStat-1.0.1.pkg

# 2) suba o instalador NESTE repositório (Release):
gh release create v1.0.1 MagicStat-1.0.1.pkg --repo heuryferr/MagicStat-Releases \
    --title "Magic Stat 1.0.1" --notes "..."

# 3) suba o updates/manifest.json atualizado (gerado no passo 1) para cá:
cd /tmp/MagicStat-Releases   # clone deste repo
cp <código>/updates/manifest.json updates/manifest.json
git add updates/manifest.json && git commit -m "publica 1.0.1" && git push
```

## Regras

- **Nunca** suba código-fonte, dados de clientes ou chaves aqui.
- O `manifest.json` é o "índice" do app: versão mais nova + link + hash do
  instalador + a mensagem que o usuário vê.
- Repositórios: código 🔒 privado (`Magic-Stat`) · distribuição 🌐 público
  (`MagicStat-Releases`).

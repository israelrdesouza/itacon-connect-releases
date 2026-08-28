# ITACON Connect — Releases

Este repositório é **exclusivamente de distribuição** do ITACON Connect
(cliente de conexão automática às pastas de rede do ITACON, escritório
e VPN).

## O que tem aqui

- Manifestos de atualização (`manifest-stable.json`, `manifest-pilot.json`)
  consumidos pelo mecanismo de auto-update do próprio aplicativo.
- Pacotes `.zip` publicados como [GitHub Releases](../../releases) deste
  repositório, referenciados pelos manifestos.

## O que **não** tem aqui

- **Código-fonte** — este repositório não contém nenhum arquivo de código do
  ITACON Connect. O desenvolvimento acontece em repositório privado
  separado.
- **Segredos** — nenhuma senha, token, chave de autenticação (ex.: Tailscale
  auth key), credencial de rede/SMB ou configuração privada de cliente é
  publicada aqui, em nenhum arquivo, commit ou release.

## Canais

| Canal | Arquivo | Uso |
|---|---|---|
| `stable` | `manifest-stable.json` | Produção — máquinas de uso diário (Jorge, Andrea). Só recebe uma versão depois que ela foi validada no canal `pilot` e promovida manualmente. |
| `pilot` | `manifest-pilot.json` | Validação — testes internos (Israel) antes de qualquer promoção para `stable`. |

O aplicativo só aplica uma atualização vinda do canal que corresponde ao
`Channel` configurado na instalação (`config.json` local) — um build
`pilot` nunca se autoatualiza a partir do manifesto `stable`, e vice-versa.

## Formato do manifesto

```json
{
  "version": "2.0.1",
  "channel": "pilot",
  "downloadUrl": "https://github.com/israelrdesouza/itacon-connect-releases/releases/download/vX.Y.Z/ItaconConnect_X.Y.Z.zip",
  "sha256": "<sha256 hex do pacote .zip, 64 caracteres>",
  "releaseNotes": "Resumo curto do que mudou.",
  "mandatory": false
}
```

Regras de segurança aplicadas pelo cliente antes de qualquer download:

- `downloadUrl` precisa ser **HTTPS** — qualquer outro esquema é recusado.
- `sha256` é recalculado sobre o pacote baixado e comparado byte a byte com
  o valor publicado aqui; se divergir, a atualização é abortada e o
  arquivo é descartado sem ser aplicado.
- `version` precisa ser semver válido (`MAJOR.MINOR.PATCH`).

## Processo de release

1. Build local validado (testes + build limpos).
2. Pacote `.zip` gerado com o conteúdo esperado pelo Updater (arquivos do
   app na raiz do zip, sem pasta extra).
3. SHA-256 do `.zip` calculado.
4. GitHub Release criada neste repositório com o `.zip` anexado.
5. Manifesto do canal correspondente atualizado com a URL da release e o
   SHA-256 real.
6. Canal `pilot` primeiro, sempre. Promoção para `stable` é manual e
   deliberada — nunca automática.

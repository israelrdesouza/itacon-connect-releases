# ITACON Connect — Releases

Este repositório é **exclusivamente de distribuição** do ITACON Connect
(cliente de conexão automática às pastas de rede do ITACON, escritório
e VPN).

## O que tem aqui

- Manifestos de atualização (`manifest-stable.json`, `manifest-pilot.json`)
  consumidos pelo mecanismo de auto-update do próprio aplicativo.
- **Instalador oficial único** (`ITACON-Connect-Setup-X.Y.Z.exe`, Inno
  Setup) publicado como [GitHub Releases](../../releases) deste
  repositório, referenciado pelos manifestos — o mesmo `.exe` serve tanto
  para instalação nova quanto para atualização de uma instalação
  existente (detecção automática pelo próprio instalador). Formato `.zip`
  usado até a v2.0.1-pilot foi descontinuado a partir da v2.0.2.

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
  "version": "2.0.2",
  "channel": "pilot",
  "downloadUrl": "https://github.com/israelrdesouza/itacon-connect-releases/releases/download/vX.Y.Z/ITACON-Connect-Setup-X.Y.Z.exe",
  "sha256": "<sha256 hex do instalador .exe, 64 caracteres>",
  "releaseNotes": "Resumo curto do que mudou.",
  "mandatory": true
}
```

Regras de segurança aplicadas pelo cliente antes de qualquer download:

- `downloadUrl` precisa ser **HTTPS** — qualquer outro esquema é recusado.
- `sha256` é recalculado sobre o instalador baixado e comparado byte a
  byte com o valor publicado aqui; se divergir, a atualização é abortada,
  o instalador **não é executado** e o arquivo é descartado.
- `version` precisa ser semver válido (`MAJOR.MINOR.PATCH`).
- Quando `mandatory: true`, o app mostra um diálogo pedindo confirmação
  explícita ("Instalar agora" / "Sair") antes de baixar qualquer coisa —
  nunca baixa/instala silenciosamente sem o usuário decidir.

## Processo de release

1. Build local validado (testes + build limpos).
2. Instalador único gerado com o Inno Setup (`installer/
   ItaconConnectColaborador.iss`) — mesmo `.exe` para instalação nova e
   atualização, `AppId` fixo permite ao Inno Setup detectar sozinho uma
   instalação existente.
3. SHA-256 do `.exe` calculado.
4. GitHub Release criada neste repositório com o `.exe` anexado.
5. Manifesto do canal correspondente atualizado com a URL da release e o
   SHA-256 real.
6. Canal `pilot` primeiro, sempre. Promoção para `stable` é manual e
   deliberada — nunca automática.

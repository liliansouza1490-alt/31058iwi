# Como compilar no GitHub (Actions)

## 1. Subir o projeto pro GitHub

1. Cria um repositório novo no GitHub (pode ser privado).
2. No terminal (na pasta do projeto):

```bash
git init
git add .
git commit -m "3105 full UI + worse_query"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/SEU_REPO.git
git push -u origin main
```

Ou arrasta a pasta no site do GitHub (upload).

## 2. Workflow já está pronto

Arquivo:

`.github/workflows/build-ios.yml`

Ele:
- Roda em `macos-14`
- Compila o target `ThreeOneOSFive` em **Release**
- **Sem assinatura** (`CODE_SIGNING_ALLOWED=NO`)
- Gera `X.ipa` (não assinado)
- Publica o artefato `x-ios-ipa` por 14 dias

## 3. Rodar o build

### Opção A – Automático
Todo `push` na `main` já dispara o workflow.

### Opção B – Manual
1. Abre o repo no GitHub
2. Aba **Actions**
3. Clica em **Build iOS app**
4. Clica em **Run workflow** → **Run workflow**

## 4. Baixar a IPA

1. Vai em **Actions**
2. Clica no run que terminou (verde)
3. Em **Artifacts** baixa **x-ios-ipa**
4. Dentro tem o `X.ipa`

## 5. Assinar e instalar no iPhone

A IPA do Actions **não é assinada**. Você precisa assinar:

### Com Sideloadly / 3uTools / iOS App Signer
- Abre o `X.ipa`
- Escolhe teu certificado Apple (conta free ou paid)
- Bundle ID: deixe `com.apple.mobile.MobileHouseArrest` se possível (ou o que teu perfil permitir)
- Se for usar **worse_query** de verdade, o App Group precisa estar no provisioning

### Com Xcode (local)
1. Abre `ThreeOneOSFive.xcodeproj` no Mac
2. Seleciona teu Team em **Signing & Capabilities**
3. Product → Archive → Distribute App

## 6. Secrets (opcional)

Se o app usar token de sync de patches:

1. Repo → **Settings** → **Secrets and variables** → **Actions**
2. New repository secret
3. Nome: `PATCH_SYNC_TOKEN`
4. Valor: o token do backend

(O workflow atual não exige esse secret pra só gerar a IPA.)

## 7. Problemas comuns

| Erro | Solução |
|------|---------|
| No schemes | Confere se o `.xcodeproj` subiu inteiro |
| Code signing error | O workflow já desliga signing; se ainda der, limpa DerivedData no runner (já usa `-derivedDataPath build`) |
| Scheme not found | Abre no Xcode local uma vez e marca o scheme como shared |
| IPA não instala | Normal: precisa assinar com certificado válido |

## Resumo rápido

```text
1. Push do projeto pro GitHub
2. Actions → Build iOS app → Run workflow
3. Baixa o artifact x-ios-ipa
4. Assina o X.ipa (Sideloadly / Xcode)
5. Instala no iPhone
```

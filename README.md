# ESP32-AutoDeploy

## Descrição do Projeto:
"ESP32-AutoDeploy é um projeto destinado a facilitar o desenvolvimento e a implementação de software em dispositivos ESP32. Utilizando GitHub Actions, o projeto automatiza o processo de deploy de firmware e código para ESP32, permitindo que desenvolvedores foquem mais no desenvolvimento e menos na gestão de deploys. Este sistema é ideal para aplicações IoT, projetos educacionais ou qualquer ambiente que beneficie de atualizações rápidas e consistentes."

## Tecnologias Envolvidas

### **MicroPython**
[MicroPython](https://micropython.org/) é uma implementação eficiente de Python 3 otimizada para rodar em microcontroladores, como o ESP32. É ideal para programação rápida e desenvolvimento de IoT.

### **ESP32**
O [ESP32](https://www.espressif.com/en/products/socs/esp32) é um microcontrolador com WiFi e Bluetooth integrados, popular entre desenvolvedores de IoT devido à sua capacidade de processamento e recursos de conectividade.

### **GitHub Actions**
[GitHub Actions](https://github.com/features/actions) é uma CI/CD que facilita a automação de pipelines de software diretamente no GitHub. Neste projeto, usamos Actions para automatizar a compilação de firmware e seu deploy.

### **esptool**
[esptool](https://github.com/espressif/esptool) é uma ferramenta Python que possibilita a comunicação com o bootloader do ESP32. Utilizamos para gerar binários .bin a partir de scripts de MicroPython.

### **OTA (Over-The-Air) Updates**
OTA permite atualizar dispositivos remotamente, sem necessidade de conexão física. O ESP32 pode buscar binários .bin diretamente do GitHub, simplificando updates. Saiba mais sobre [OTA Updates para ESP32](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/system/ota.html).

## Como Contribuir

Seu apoio e contribuições são muito bem-vindos! Sinta-se à vontade para fazer um fork do projeto, sugerir melhorias através de issues, ou enviar pull requests. Aqui estão algumas maneiras de contribuir:

- **Reportar Bugs**
- **Sugerir Novas Funcionalidades**
- **Melhorar a Documentação**
- **Escrever Testes**
- **Espalhar a Palavra**

## Feito por

Rogerio Alencar Filho

## Redes Sociais

- Siga-me no Instagram: [@rogerioalencarfilho](https://www.instagram.com/rogerioalencarfilho)


---

# Iniciando com GitHub Actions

GitHub Actions é uma plataforma de automação que permite executar workflows diretamente no seu repositório GitHub. Você pode usar Actions para automatizar, personalizar e executar seus processos de desenvolvimento de software.

## Passo 1: Criar o Arquivo de Workflow

1. **Navegue até seu repositório no GitHub.**
2. **Vá até a aba "Actions" e clique em "New workflow".**
3. **Você pode começar com um template ou criar um arquivo `.yml` manualmente na pasta `.github/workflows` do seu repositório.**

## Passo 2: Definir o Workflow

Aqui está um exemplo básico de arquivo `.yml` para compilar um projeto MicroPython para o ESP32:

```yaml
name: Build and Deploy Firmware

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.x'

      - name: Install esptool
        run: |
          python -m pip install --upgrade pip
          pip install esptool

      - name: Extract Version from config.json
        id: config
        run: |
          echo "Extracting version..."
          VERSION=$(cat config.json | grep version | cut -d '"' -f 4)
          echo "::set-output name=version::$VERSION"

      - name: Create firmware binary
        run: |
          # Simulate building process (replace this with your actual build command) 
          #  esptool.py --chip esp32 elf2image --output my_firmware.bin

          echo "Building firmware for version ${{ steps.config.outputs.version }}"
          touch esp32-autodeploy-${{ steps.config.outputs.version }}.bin

      - name: Upload firmware to /firmware directory
        run: |
          mkdir -p firmware
          mv esp32-autodeploy-${{ steps.config.outputs.version }}.bin firmware/
      
      - name: Commit firmware binary
        run: |
          git config --local user.email "action@github.com"
          git config --local user.name "GitHub Action"
          git add firmware/
          git commit -m "Deploy firmware version ${{ steps.config.outputs.version }}"
          git push
```

## Passo 3: Configurar Secrets

1. **Vá para "Settings" no seu repositório GitHub.**
2. **Clique em "Secrets" e então "New repository secret".**
3. **Adicione as credenciais necessárias, como senhas, tokens de acesso, etc., para evitar colocá-las diretamente no seu código.**

## Passo 4: Acompanhar a Execução do Workflow

- **Após criar seu workflow, cada push ou pull request para a branch especificada irá disparar o workflow.**
- **Você pode acompanhar a execução do workflow na aba "Actions" do seu repositório.**

## Passo 5: Adaptar e Expandir o Workflow

- **Você pode expandir seu workflow adicionando etapas de testes, análises de código, notificações e mais.**
- **Adapte o workflow conforme a necessidade do projeto, modificando as etapas ou adicionando novas conforme necessário.**

---

Bloquear a branch `main` para garantir que todos os pull requests sejam revisados e aprovados antes de serem mesclados é uma prática comum para manter a qualidade e a integridade do código. Aqui está um guia passo a passo sobre como configurar isso no GitHub, usando as proteções de branch:

---

# Como Configurar Proteções de Branch no GitHub

Proteger sua branch `main` impede que alterações diretas sejam feitas, exigindo revisões de pull request antes de qualquer mesclagem. Isso é essencial para manter um fluxo de trabalho de desenvolvimento estável e seguro.

## Passo 1: Acessar as Configurações do Repositório

1. Navegue até o seu repositório no GitHub.
2. Clique em **Settings** na barra de navegação superior.

## Passo 2: Configurar as Proteções de Branch

1. No menu lateral, clique em **Branches** sob a seção **Code and automation**.
2. Encontre a seção **Branch protection rules** e clique em **Add rule**.

## Passo 3: Definir a Proteção para a Branch `main`

1. No campo **Branch name pattern**, digite `main` para aplicar a regra à branch `main`.
2. Marque a caixa **Require pull request reviews before merging**. Isso requer que pelo menos uma revisão de pull request seja aprovada antes que as mudanças possam ser mescladas na branch protegida.
3. (Opcional) Marque **Require review from Code Owners** se você tiver um arquivo CODEOWNERS no repositório para exigir revisão dos proprietários do código.
4. (Opcional) Marque **Require status checks to pass before merging** se você deseja que certos checks passem antes que a mesclagem possa ocorrer. Você pode selecionar checks específicos que deseja exigir.
5. (Opcional) Marque **Include administrators** para aplicar essas regras também aos administradores do repositório.
6. Clique em **Create** ou **Save changes** para ativar a proteção da branch.

## Passo 4: Testar as Configurações

Para garantir que a proteção de branch está funcionando como esperado:
- Tente fazer um push diretamente para a `main`. Você deve receber uma mensagem de erro.
- Abra um pull request e tente mesclá-lo sem uma revisão. O GitHub não deve permitir a mesclagem até que as condições configuradas sejam cumpridas.



Essas configurações ajudarão a garantir que todas as mudanças na branch `main` sejam revisadas e aprovadas, mantendo a qualidade e a segurança do código no seu projeto.
<div align="center">
  <img src="https://img.shields.io/badge/Plataforma-Arduino_Uno-00979D?style=for-the-badge&logo=arduino&logoColor=white" alt="Arduino Uno">
  <img src="https://img.shields.io/badge/Simulador-Wokwi-2196F3?style=for-the-badge&logo=virtualbox&logoColor=white" alt="Wokwi">
  <img src="https://img.shields.io/badge/Status-Conclu%C3%ADdo-brightgreen?style=for-the-badge" alt="Status Concluído">
</div>

# 📑 Checkpoint 2 (CP2) – Engenharia de Edge Computing
### 🌐 Monitoramento Climático Inteligente com Driver I2C e Sensores Estabilizados

---

## 👥 Integrantes do Grupo (Nextcode)

| Nome do Integrante | RM |
| :--- | :--- |
| **Kaick Lima Silva** | `RM: 574060` |
| **Gustavo Basso** | `RM: 572623` |
| **Guilherme Sales** | `RM: 572933` |
| **Pedro Feltrin Geraldes** | `RM: 569038` |
| **Guilherme Kozikoski Failla** | `RM: 571611` |

---

## 📝 Descrição do Projeto

Este projeto consiste em um sistema microcontrolado de **Edge Computing** voltado ao monitoramento e à triagem automatizada de variáveis climáticas locais. Utilizando a plataforma **Arduino Uno**, o sistema realiza a aquisição em tempo real de:

* 💡 **Luminosidade Relativa** (via transdutor resistivo LDR)
* 🌡️ **Temperatura Ambiente** (via sensor térmico semicondutor)
* 💧 **Umidade Relativa do Ar** (via sensor higrométrico)

Os sinais coletados passam por um estágio de tratamento estatístico interno (médias móveis de rejeição de ruído) antes de alimentar uma máquina de estados responsável por acionar sinalizações visuais através de uma matriz de LEDs, alertas sonoros e exibição parametrizada em um **Display LCD 16x2 com barramento I2C**.
Existem 2 códigos dentro do projeto, onde o 1 é o projeto final, realizando uma varredura completa do sistema, e tratando os leds como um conjunto, onde se existe um erro, os leds reagem de acordo com o erro, não permitindo que o **Led Verde** Seja acesso enquanto os parámetros não forem ajustados. Como exemplo, se existe problemas na temperatura, mas a luminosidade e humidade estão OK, ele não irá permitir que o Led Verde se ascenda.

Já o segundo código dentro do "2-Código.txt", que roda individualmente cada cénario, permitindo uma visão individual no sistema, entretanto, podendo trazer uma falsa sensação de segurança, pois o led visualmente, mostra o momento da leitura, ou seja, se na leitura da luminosidade estiver com os parámetros dentro do permitido, ascende o led Verde, mas a humidade pode não estar dentro dos parámetros.

---

## 🛠️ Componentes e Conexões (Hardware)

> 💡 **Padronização:** Siga o mapeamento de barramentos e as cores sugeridas de jumpers para facilitar o diagnóstico visual do circuito.

| Componente | Pino Arduino | Cor Jumper | Categoria do Sinal | Função Técnica no Sistema |
| :--- | :---: | :---: | :---: | :--- |
| **VCC (Alimentação)** | `5V` | 🔴 Vermelho | Alimentação | Barramento positivo comum (+5V DC) |
| **GND (Terra)** | `GND` | 🟤 Marrom | Alimentação | Referência de aterramento comum (0V) |
| **LDR (Luminosidade)** | `A0` | 🟡 Amarelo | Analógico | Sensor resistivo de variação luminosa |
| **DHT-11 / DHT-22** | `Digital 6` | 🟠 Laranja | Digital (Single-Bus) | Entrada de dados seriais de clima |
| **LCD 16x2 I2C (SDA)** | `Analógico A4` | 🟡 Amarelo | I2C (Dados) | Linha de dados bidirecional serial |
| **LCD 16x2 I2C (SCL)** | `Analógico A5` | 🟡 Amarelo | I2C (Clock) | Linha de pulso de clock síncrono |
| **LED Vermelho** | `Digital 10` | 🟠 Laranja | Saída Digital | Alerta de nível crítico / Inconformidade grave |
| **LED Amarelo** | `Digital 9` | 🟠 Laranja | Saída Digital | Alerta de transição ou nível de atenção |
| **LED Verde** | `Digital 8` | 🟠 Laranja | Saída Digital | Sinalizador de estabilidade e conformidade |
| **Buzzer** | `Digital 7` | 🟠 Laranja | Saída PWM/Frequência | Transdutor piezoelétrico para alarmes sonoros |

---

## 📐 Regras de Funcionamento e Condicionais

### ⚙️ Regras Gerais de Firmware
* 📊 **Filtro Antitravamento:** Cada valor exibido na tela representa a **média aritmética exata de 5 amostras** consecutivas coletadas do sensor.
* ⏱️ **Janela de Varredura:** O display alterna e atualiza suas telas estritamente a cada **5 segundos**, garantindo o tempo de resposta térmico dos componentes.

### 🎛️ Matriz de Estados e Tomada de Decisão

<details>
<summary><b>👁️ Clique para expandir as Condições de Luminosidade (LDR)</b></summary>

* **Cenário Escuro:**
  * 🟢 `LED Verde`: **LIGADO** | 🟡 `LED Amarelo`: DESLIGADO | 🔴 `LED Vermelho`: DESLIGADO
  * 🔊 `Buzzer`: DESLIGADO | 📺 `LCD`: Exibe dados estáveis de ambiente escuro.
* **Cenário Meia Luz:**
  * 🟢 `LED Verde`: DESLIGADO | 🟡 `LED Amarelo`: **LIGADO** | 🔴 `LED Vermelho`: DESLIGADO
  * 🔊 `Buzzer`: DESLIGADO | 📺 `LCD`: Mensagem fixa `Ambiente a meia luz`.
* **Cenário Totalmente Iluminado:**
  * 🟢 `LED Verde`: DESLIGADO | 🟡 `LED Amarelo`: DESLIGADO | 🔴 `LED Vermelho`: **LIGADO**
  * 🔊 `Buzzer`: **ATIVADO CONTINUAMENTE** (440Hz) | 📺 `LCD`: Mensagem fixa `Ambiente muito claro`.
</details>

<details>
<summary><b>🌡️ Clique para expandir as Condições de Temperatura (DHT)</b></summary>

* **Faixa Ideal (`10°C` até `15°C`):**
  * 🟡 `LED Amarelo`: DESLIGADO | 🔊 `Buzzer`: DESLIGADO
  * 📺 `LCD`: Exibe `Temperatura OK` e o valor numérico em tempo real.
* **Condição de Inconformidade Térmica (Fora da Faixa):**
  * 🟡 `LED Amarelo`: **LIGADO** | 🔊 `Buzzer`: **ATIVADO CONTINUAMENTE**
  * 🔺 *Se superior a 15°C:* 📺 `LCD` exibe `Temp. Alta` + Valor.
  * 🔻 *Se inferior a 10°C:* 📺 `LCD` exibe `Temp. Baixa` + Valor.
</details>

<details>
<summary><b>💧 Clique para expandir as Condições de Umidade Relativa (DHT)</b></summary>

* **Faixa Ideal (`50%` até `70%`):**
  * 🔴 `LED Vermelho`: DESLIGADO | 🔊 `Buzzer`: DESLIGADO
  * 📺 `LCD`: Exibe `Umidade OK` e o valor percentual correspondente.
* **Condição de Inconformidade de Umidade (Fora da Faixa):**
  * 🔴 `LED Vermelho`: **LIGADO** | 🔊 `Buzzer`: **ATIVADO CONTINUAMENTE**
  * 🔺 *Se superior a 70%:* 📺 `LCD` exibe `Umidade. Alta` + Valor.
  * 🔻 *Se inferior a 50%:* 📺 `LCD` exibe `Umidade. Baixa` + Valor.
</details>

---

## 📦 Dependências e Bibliotecas

Para efetuar a compilação e o carregamento do código-fonte, instale as seguintes dependências no **Gerenciador de Bibliotecas da Arduino IDE**:

1. **LiquidCrystal I2C** *(Por Frank de Brabander)* – Controle do display via protocolo I2C.
2. **DHT sensor library** *(Por Adafruit)* – Processamento de sinais do sensor DHT.
3. **Adafruit Unified Sensor** *(Por Adafruit)* – Biblioteca base obrigatória para os drivers da Adafruit.

---

## 🔧 Instruções de Reprodução e Execução

### 🌐 Modo Simulação (Ambiente Virtual)
O circuito completo e funcional pode ser executado diretamente pelo navegador:
* 🔗 **Link Wokwi:** [Acessar Simulação do Projeto](https://wokwi.com/projects/464224358385697793)


### 💻 Modo Hardware Real (Computador Local)
Para transferir o projeto para a sua placa física do Arduino Uno, siga os passos abaixo:

1. **Download do Arquivo:** Baixe o arquivo de código-fonte `CP2.EDGE.ino` deste repositório.
2. **Preparação do Software:** Abra o arquivo baixado utilizando a **Arduino IDE**.
3. **Instalação das Bibliotecas:** Certifique-se de ter instalado as dependências listadas na seção anterior.
4. **Montagem Física:** Conecte os componentes na protoboard seguindo fielmente a tabela de **Componentes e Ligações**.
5. **Configuração da Placa:** No menu da IDE, acesse `Ferramentas` -> `Placa` e selecione **Arduino Uno**. Depois, escolha a porta `COM` correspondente ao seu cabo USB.
6. **Upload do Firmware:** Clique no botão **Carregar** (ícone de seta para a direita). Abra o Monitor Serial (`9600 bps`) para acompanhar a depuração dos dados.


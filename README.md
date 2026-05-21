# 📑 Checkpoint 2 (CP2) – Engenharia de Edge Computing
### Implementação do Sensor DHT e Display LCD I2C

---

## 👥 Integrantes do Grupo (Nextcode)
* **Kaick Lima Silva** (RM: 574060)
* **Gustavo Basso** (RM: 572623)
* **Guilherme Sales** (RM: 572933)
* **Pedro Feltrin Geraldes** (RM: 569038)
* **Guilherme Kozikoski Failla** (RM: 571611)

---

## 📝 Descrição do Projeto
Este projeto consiste em um sistema de monitoramento ambiental automatizado desenvolvido para a plataforma Arduino Uno. O dispositivo realiza a captura contínua de variáveis físicas como luminosidade (via sensor LDR), temperatura e umidade relativa do ar (via sensor DHT). 

Os sinais analógicos e digitais coletados passam por um tratamento de dados via firmware (cálculo de médias aritméticas) para tomada de decisões lógicas em tempo real. O sistema gerencia uma matriz de alertas visuais (LEDs Verde, Amarelo e Vermelho), alertas sonoros baseados em frequência (Buzzer) e exibe o status de operação de maneira dinâmica e alternada em um Display LCD 16x2 com barramento I2C.

---

## 🛠️ Componentes e Conexões (Hardware)

A organização das pinagens físicas e o mapeamento de cores dos condutores elétricos foram estruturados da seguinte forma:

| Componente | Pino no Arduino | Cor do Jumper Base | Função do Componente |
| :--- | :--- | :--- | :--- |
| **VCC (Alimentação)** | 5V | Vermelho | Linha de energia positiva do sistema |
| **GND (Terra)** | GND | Marrom | Linha de aterramento comum (referência) |
| **LDR (Luminosidade)** | A0 | Amarelo | Entrada analógica para leitura de luz |
| **DHT-11 / DHT-22** | Digital 6 | Laranja | Linha de dados (Single-Bus) do sensor de clima |
| **LCD 16x2 I2C (SDA)** | Analógico A4 | Amarelo | Linha de dados bidirecional do barramento I2C |
| **LCD 16x2 I2C (SCL)** | Analógico A5 | Amarelo | Linha de clock de sincronia do barramento I2C |
| **LED Vermelho** | Digital 10 | Laranja | Alerta de nível crítico / Emergência ambiental |
| **LED Amarelo** | Digital 9 | Laranja | Alerta de nível de atenção / Transição |
| **LED Verde** | Digital 8 | Laranja | Indicador de conformidade (Sistema Seguro) |
| **Buzzer** | Digital 7 | Laranja | Transdutor sonoro para alarmes contínuos |

---

## 📐 Regras de Funcionamento do Sistema

### ⚙️ Regras Gerais de Firmware
* **Estabilização de Dados:** Para mitigar ruídos elétricos, os valores apresentados no display são calculados com base na **média aritmética de pelo menos 5 leituras consecutivas** de cada sensor.
* **Tempo de Amostragem:** As informações são processadas e alternadas no ecrã em ciclos estáveis a cada **5 segundos**.

### 💡 Sensor LDR (Luminosidade)
* 🟢 **Ambiente Escuro:** Apenas o **LED Verde** deve permanecer aceso.
* 🟡 **Ambiente a Meia Luz:** O **LED Amarelo** é acionado e o display exibe: `Ambiente a meia luz`.
* 🔴 **Ambiente Totalmente Iluminado:** O **LED Vermelho** acende, o display exibe `Ambiente muito claro` e o **Buzzer** é ativado continuamente.

### 🌡️ Sensor DHT (Temperatura)
* 🔹 **Faixa Ideal (Entre 10°C e 15°C):** O display informa `Temperatura OK` acompanhado do valor em tempo real.
* ⚠️ **Fora da Faixa Ideal:** O **LED Amarelo** e o **Buzzer** são ligados de forma contínua.
  * **Acima de 15°C:** O display exibe a mensagem de advertência `Temp. Alta`.
  * **Abaixo de 10°C:** O display exibe a mensagem de advertência `Temp. Baixa`.

### 💧 Sensor DHT (Umidade)
* 🔹 **Faixa Ideal (Entre 50% e 70%):** O display informa `Umidade OK` acompanhado do valor percentual.
* ⚠️ **Fora da Faixa Ideal:** O **LED Vermelho** e o **Buzzer** são ligados de forma contínua.
  * **Acima de 70%:** O display exibe a mensagem de advertência `Umidade. Alta`.
  * **Abaixo de 50%:** O display exibe a mensagem de advertência `Umidade. Baixa`.

---

## ⚠️ Desafios Técnicos e Observações de Implementação

Durante o desenvolvimento física e lógico da CP2, foram superados os seguintes obstáculos de engenharia:

* **Disponibilidade de Componentes no Simulador:** O software Wokwi e o Tinkercad não possuem o modelo de sensor DHT-11 nativo de forma estável para simulação direta no Arduino Uno. Para contornar a limitação de software mantendo a fidelidade das portas e barramentos, foi adotado o **DHT-22**, o qual utiliza a mesma arquitetura de comunicação (*Single-Wire*) e bibliotecas compatíveis.
* **Tratamento das Variáveis de Luz:** A conversão de grandezas de iluminamento físico (Lux) para valores percentuais legíveis demandou o uso da função matemática `map()`, protegendo as saídas contra estouros de variáveis e travamentos de tela em transições bruscas de ambiente.
* **Tratamento de Falhas Críticas (`NaN`):** Os sensores da família DHT requerem uma janela de tempo específica para realizar a resposta física e a conversão interna. Caso consultados em intervalos muito curtos, geram erros do tipo `NaN` (Dado Não Numérico). Isso foi solucionado implementando filtros lógicos com a função `!isnan(variavel)` e ajustando um `delay(1400)` fixo que, somado às amostras prévias, estabiliza o tempo do ciclo completo em 5 segundos.
* **Endereçamento Hexadecimal I2C:** Displays LCD reais podem apresentar o endereço físico de comunicação em `0x27` ou `0x3F` dependendo do fabricante do chip expansor (PCF8574). No protótipo real, recomenda-se rodar um firmware do tipo *I2C Scanner* para validar a linha `#define ende 0x27` antes de energizar o display de forma definitiva.
* **Mau Contato em Alta Densidade de Conexões:** O volume considerável de componentes e conexões na protoboard eleva o risco de mau contato. A separação estrita de cores dos jumpers ajudou no rastreio rápido de falhas (*troubleshooting*).

---

## 📦 Dependências e Bibliotecas

Para efetuar a compilação e carregamento do código fonte, é obrigatório possuir as seguintes dependências instaladas no Gestor de Bibliotecas da sua **Arduino IDE**:

1. **LiquidCrystal I2C** *(Desenvolvida por Frank de Brabander)* - Para controle do display via protocolo de dois fios.
2. **DHT sensor library** *(Desenvolvida por Adafruit)* - Para processamento dos sinais do sensor de temperatura e umidade.
3. **Adafruit Unified Sensor** *(Desenvolvida por Adafruit)* - Biblioteca base obrigatória para os drivers de sensores da Adafruit.

---

## 🔧 Instruções de Reprodução e Execução

### 🌐 Modo Simulação (Ambiente Virtual)
O circuito completo e funcional pode ser executado diretamente pelo navegador através do link oficial de simulação do grupo:
* **Link Wokwi:** [Acessar Simulação do Projeto](https://wokwi.com/projects/464224358385697793)

### 💻 Modo Hardware Real (Computador local)
Para transferir o código do projeto para a sua placa física do Arduino Uno, siga os passos abaixo:

1. **Download do Arquivo:** Baixe o arquivo de código fonte do repositório chamado `CP2.EDGE.ino`.
2. **Preparação do Software:** Abra o arquivo baixado utilizando a **Arduino IDE**.
3. **Instalação dos Drivers:** Garanta que todas as bibliotecas listadas na seção de **Dependências** estejam baixadas e ativas.
4. **Montagem Física:** Conecte os componentes na protoboard seguindo fielmente as portas listadas na tabela de **Componentes e Ligações**.
5. **Configuração da Placa:** No menu superior da IDE, acesse *Ferramentas -> Placa* e selecione **Arduino Uno**. Em seguida, selecione a porta **COM** correspondente onde seu cabo USB está conectado.
6. **Upload do Firmware:** Clique no botão **Upload** (ícone de seta para a direita) para compilar o código e gravá-lo no microcontrolador. Abra o Monitor Serial (9600 bps) se desejar acompanhar a depuração de dados por texto.

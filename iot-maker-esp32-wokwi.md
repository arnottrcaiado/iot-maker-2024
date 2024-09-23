
# ESP32 e IoT: Explorando o Potencial da Placa Espressif

## Introdução à Placa ESP32

A placa ESP32, desenvolvida pela Espressif Systems, é uma das mais poderosas e versáteis plataformas de prototipagem para projetos de IoT. Com recursos avançados de conectividade Wi-Fi e Bluetooth, além de alta capacidade de processamento e baixo consumo de energia, a ESP32 se tornou uma escolha popular entre desenvolvedores e entusiastas de eletrônica.

### Origem e Importância

A Espressif Systems é uma empresa de semicondutores chinesa fundada em 2008, conhecida por criar microcontroladores com conectividade integrada. O ESP8266, seu primeiro sucesso, revolucionou o mercado ao oferecer conectividade Wi-Fi a um custo muito baixo. A evolução natural desse projeto resultou na ESP32, que trouxe melhorias significativas em termos de desempenho, recursos e integração.

#### Principais Características da ESP32

- **Duas Tensões de Operação:** A ESP32 opera em 3.3V, diferentemente do Arduino, que opera em 5V. Isso exige cuidados na conexão com outros componentes.
- **Múltiplos Núcleos:** Possui um processador dual-core de 32 bits (Tensilica Xtensa LX6), permitindo multitarefa e maior capacidade de processamento.
- **Conectividade Avançada:** Suporte nativo para Wi-Fi e Bluetooth, facilitando a criação de dispositivos conectados.
- **Baixo Consumo de Energia:** Modos de operação com diferentes níveis de consumo, ideais para aplicações que exigem eficiência energética.

## Arquitetura da ESP32

A arquitetura da ESP32 inclui uma série de componentes que tornam a placa extremamente flexível e poderosa:

- **Processador Dual-core:** Permite a execução simultânea de tarefas em dois núcleos independentes.
- **Memória RAM:** A ESP32 possui até 520 KB de SRAM, suficiente para a maioria das aplicações.
- **Flash Interna:** Geralmente de 4 MB, permitindo o armazenamento de firmware e dados.
- **Conectores GPIO:** Mais de 30 pinos configuráveis como entrada ou saída, compatíveis com sinais digitais e analógicos.
- **Interfaces de Comunicação:** Inclui I2C, SPI, UART, ADC, DAC e PWM, facilitando a conexão com diversos sensores e atuadores.

## Diferenças de Pinagem e Voltagem

### Pinagem

A ESP32 possui mais pinos GPIO em comparação com a maioria das placas Arduino, permitindo maior flexibilidade em projetos complexos. É importante notar que alguns pinos têm funcionalidades especiais, como entrada analógica, conversão digital para analógico (DAC) e pulsos de largura modulada (PWM).

[Pinagem ESP32](https://blog.eletrogate.com/conhecendo-o-esp32-introducao-1/)

### Voltagem

A ESP32 opera em 3.3V, enquanto o Arduino usa 5V. Isso significa que é necessário cuidado ao conectar dispositivos que operam a 5V diretamente nos pinos da ESP32, pois isso pode danificar o microcontrolador.

## Exemplos de Projetos Simples com ESP32

### Projeto 1: Acender um LED com ESP32 (Sem IoT)

Este exemplo mostra como acender um LED usando a ESP32. O código é simples e utiliza a linguagem C padrão para microcontroladores.

```c
// Acende um LED conectado ao pino 2 da ESP32

#include <Arduino.h>

void setup() {
  pinMode(2, OUTPUT); // Define o pino 2 como saída
}

void loop() {
  digitalWrite(2, HIGH); // Liga o LED
  delay(1000);           // Aguarda 1 segundo
  digitalWrite(2, LOW);  // Desliga o LED
  delay(1000);           // Aguarda 1 segundo
}
```

### Projeto 2: Sensor de Temperatura com DHT11 e Monitor Serial

Neste exemplo, conectamos um sensor DHT11 à ESP32 para medir temperatura e umidade, exibindo os resultados no monitor serial.

```c
#include <DHT.h>

#define DHTPIN 4    // Pino onde o sensor está conectado
#define DHTTYPE DHT11 // Define o tipo de sensor (DHT11)

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  dht.begin();
}

void loop() {
  float h = dht.readHumidity();    // Lê a umidade
  float t = dht.readTemperature(); // Lê a temperatura

  if (isnan(h) || isnan(t)) {
    Serial.println("Falha ao ler o sensor DHT!");
    return;
  }

  Serial.print("Umidade: ");
  Serial.print(h);
  Serial.print(" %\t");
  Serial.print("Temperatura: ");
  Serial.print(t);
  Serial.println(" *C");

  delay(2000); // Aguarda 2 segundos antes de ler novamente
}
```

## Exemplos com IoT

### Projeto 3: Enviando Dados para ThingSpeak

O ThingSpeak é uma plataforma de IoT que permite armazenar e visualizar dados de sensores. Neste exemplo, enviaremos os dados de temperatura e umidade do projeto anterior para a plataforma ThingSpeak.

1. **Configuração do ThingSpeak:**
   - Crie uma conta no [ThingSpeak](https://thingspeak.com/).
   - Crie um novo canal e anote a chave de escrita (API Key).

2. **Código para Envio de Dados:**

```c
#include <WiFi.h>
#include <HTTPClient.h>
#include <DHT.h>

#define DHTPIN 4
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);

const char* ssid = "SEU_SSID";
const char* password = "SUA_SENHA";
const char* server = "http://api.thingspeak.com/update";
const char* apiKey = "SUA_API_KEY";

void setup() {
  Serial.begin(115200);
  dht.begin();
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Conectando ao WiFi...");
  }

  Serial.println("Conectado ao WiFi");
}

void loop() {
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  if (isnan(h) || isnan(t)) {
    Serial.println("Erro ao ler o sensor DHT!");
    return;
  }

  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;

    String url = String(server) + "?api_key=" + apiKey + "&field1=" + String(t) + "&field2=" + String(h);
    http.begin(url);

    int httpResponseCode = http.GET();

    if (httpResponseCode > 0) {
      Serial.println("Dados enviados para ThingSpeak com sucesso.");
    } else {
      Serial.println("Erro ao enviar os dados.");
    }

    http.end();
  }

  delay(20000); // Envia os dados a cada 20 segundos
}
```

## Simulação com Wokwi

O [Wokwi](https://wokwi.com/) é uma plataforma de simulação online que permite testar projetos com ESP32 sem a necessidade de hardware físico. Ele oferece uma interface amigável para adicionar componentes e programar a placa diretamente no navegador. A simulação no Wokwi é uma excelente maneira de validar projetos, especialmente aqueles que envolvem conectividade Wi-Fi, pois permite testar o código de forma prática e rápida.

### Como Utilizar o Wokwi para Projetos com ESP32 e Wi-Fi

1. Acesse o site [Wokwi](https://wokwi.com/).
2. Clique em "Create New Project" e selecione ESP32 como a placa.
3. Arraste componentes como LEDs, resistores e sensores para o espaço de trabalho.
4. Conecte os componentes ao ESP32 usando as portas GPIO.
5. Insira o código na aba de programação, assim como faria na IDE Arduino.

### Exemplo Real de Simulação com Wi-Fi no Wokwi

Vamos simular um projeto que conecta o ESP32 a uma rede Wi-Fi e envia dados para o ThingSpeak, assim como no exemplo anterior.

1. Adicione um sensor de temperatura DHT11, um LED e um display OLED ao ambiente do Wokwi.
2. Conecte o DHT11 ao pino GPIO 4 e o LED ao GPIO 2.
3. Use o código abaixo para conectar ao Wi-Fi e enviar dados de temperatura e umidade ao ThingSpeak.

#### Código para Simulação no Wokwi

```c
# Exemplo IoT com ESP32, DHT22 e Ultrassom no Wokwi

```c
#include <WiFi.h>
#include "DHTesp.h"
#include "ThingSpeak.h"

const int DHT_PIN = 15;                    // Pino para ligar sensor de umidade e temperatura
const int LED_PIN = 13;                    // Pino para ligar LED no ESP
#define TRIG_PIN 12                        // Pino Trig conectado ao D12
#define ECHO_PIN 18                        // Pino Echo conectado ao D18

const char* WIFI_NAME = "Wokwi-GUEST";     // Nome da rede - Wokwi-GUEST é o nome da rede no simulador
const char* WIFI_PASSWORD = "";            // Senha da rede - sem senha é o padrão do Wokwi
const int myChannelNumber = 2485063;       // Número do ID do canal no ThingSpeak
const char* myApiKey = "Q2PFOLK6HZOPW10X"; // API key gerada no ThingSpeak
const char* server = "api.thingspeak.com"; // Endpoint do ThingSpeak

// Endereço público para visualizar dados
// https://thingspeak.com/channels/2485063

DHTesp dhtSensor;
WiFiClient client;

void setup() {
  Serial.begin(115200);
  dhtSensor.setup(DHT_PIN, DHTesp::DHT22);
  pinMode(LED_PIN, OUTPUT);

  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  // Conexão com a rede Wi-Fi
  WiFi.begin(WIFI_NAME, WIFI_PASSWORD);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Conectando ao Wi-Fi...");
  }
  Serial.println("Conectado ao Wi-Fi!");
  Serial.println("IP Local: " + String(WiFi.localIP()));
  WiFi.mode(WIFI_STA);
  ThingSpeak.begin(client);
}

void loop() {
  long duration, distance;
  TempAndHumidity data = dhtSensor.getTempAndHumidity();

  // Emitir um pulso ultrassônico
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  // Medir o tempo de retorno do eco
  duration = pulseIn(ECHO_PIN, HIGH);
  // Calcular a distância em centímetros
  distance = (duration / 2) * 0.0343;

  // Enviando dados para o ThingSpeak
  ThingSpeak.setField(1, data.temperature);
  ThingSpeak.setField(2, data.humidity);
  ThingSpeak.setField(3, distance);

  // Verificar limites de temperatura e umidade para controle do LED
  if (data.temperature > 35 || data.temperature < 12 || data.humidity > 70 || data.humidity < 40) {
    digitalWrite(LED_PIN, HIGH);
  } else {
    digitalWrite(LED_PIN, LOW);
  }

  // Enviar dados para ThingSpeak
  int responseCode = ThingSpeak.writeFields(myChannelNumber, myApiKey);
  if (responseCode == 200) {
    Serial.println("Dados enviados com sucesso!");
  } else {
    Serial.println("Erro ao enviar dados: " + String(responseCode));
  }

  // Exibir dados no console
  Serial.println("Temp: " + String(data.temperature, 2) + " °C");
  Serial.println("Umidade: " + String(data.humidity, 1) + " %");
  Serial.println("Distância: " + String(distance, 1) + " cm");
  Serial.println("---");

  // Intervalo de envio de dados para conta gratuita do ThingSpeak (30 segundos)
  delay(30000);
}

```

### Instruções para Simulação:

1. Copie o código acima para o editor de código do Wokwi.
2. Execute a simulação clicando em "Start Simulation".
3. Verifique a conexão Wi-Fi e o envio de dados no console do Wokwi e no painel do ThingSpeak.

### Notas sobre a Simulação no Wokwi
Configuração do Projeto no Wokwi:

Componentes Necessários:
Sensor DHT22 conectado ao pino GPIO 15.
Sensor Ultrassônico (HC-SR04) com o pino TRIG no GPIO 12 e o pino ECHO no GPIO 18.
LED conectado ao pino GPIO 13.
Bibliotecas:
WiFi.h: Para conectar o ESP32 à rede Wi-Fi.
DHTesp.h: Para a leitura dos dados do sensor DHT22.
ThingSpeak.h: Para enviar dados ao ThingSpeak.
Conexão ao Wi-Fi:

No simulador Wokwi, o nome da rede deve ser "Wokwi-GUEST" e a senha deve estar vazia. Em um ambiente real, substitua esses valores pelos dados da sua rede Wi-Fi.
Envio de Dados ao ThingSpeak:

Certifique-se de que o canal ThingSpeak esteja configurado corretamente com os campos apropriados. Verifique o myChannelNumber e myApiKey.
Simulação:

O código simula a leitura de temperatura, umidade e distância. Os dados são enviados ao ThingSpeak a cada 30 segundos, e o LED é acionado conforme os limites de temperatura e umidade definidos.

## Conclusão

O ESP32, com sua poderosa arquitetura e capacidade de conectividade, é uma excelente escolha para projetos de IoT e prototipagem. A simulação com o Wokwi permite testar projetos complexos de forma eficiente e prática, especialmente aqueles que envolvem recursos de rede. Combinando a programação C e o uso de plataformas como ThingSpeak, é possível explorar todo o potencial da IoT de maneira prática e acessível.

## Referências

- **ESPRESSIF SYSTEMS.** ESP32 Series Datasheet. [S.l.]: Espressif, 2021. Disponível em: <https://www.espressif.com/sites/default/files/documentation/esp32_datasheet_en.pdf>. Acesso em: 23 set. 2024.
- **Site Oficial do Wokwi.** Disponível em: <https://wokwi.com/>. Acesso em: 23 set. 2024.
- **Site Oficial do ThingSpeak.** Disponível em: <https://thingspeak.com/>. Acesso em: 23 set. 2024.
- **BORGES, G. F.** IoT na Prática com ESP32 e Arduino. São Paulo: Novatec, 2022.
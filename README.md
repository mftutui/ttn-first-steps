# ttn-first-steps
Primeiros passos para o uso da plataforma The Things Network

Este é um guia iniciante para o uso da TTN. Usando o módulo [The Things UNO](https://www.thethingsnetwork.org/docs/devices/uno/) será construido um dispositipo capaz de realizar UPLINK com um sensor de temperatura e DOWNLINK usando um LED.

### Importante
📗 Leia o material todo antes de começar

☺️ Caso encontre algum erro, tenha alguma sugestão ou dúvida fique a vontade para entrar em contato

### Requisitos
* Login em [The Things Network](https://www.thethingsnetwork.org/)
* Conhecimento básico em programação e Arduino
* Cobertura LoRa (gateway)

> [Tutorial](https://github.com/mftutui/configuracoes-gateway-ttn#Registro-na-TTN) para o desenvolvimento de um gateway para uso na TTN 

### Sumário
1. [Adicionar uma aplicação](#Adicionar-uma-aplicação)

2. [Adicionar Device](#Adicionar-Device)

	1. [Instalar a IDE Arduino](#Instalar-a-IDE-Arduino)

	2. [Adicionar biblioteca The Things Network](#Adicionar-biblioteca-The-Things-Network)

	3. [Descobrir o Device EUI](#Descobrir-o-Device-EUI)

	4. [Registrar o Device na aplicação](#Registrar-o-Device-na-aplicação) 

3. [Desenvolver uma aplicação](#3Desenvolver-uma-aplicação)

	1. [Prototipagem](#Prototipagem)

	2. [Programação](#Programação)

		1. [UPLINK](#UPLINK)

		2. [DOWNLINK](#DOWNLINK)
        
## Adicionar uma aplicação

Logado a The Things Network vá até **Console** > **Application** > **add aplication**, e preencha os campos da seguinte tela:

![add_aplication](https://github.com/mftutui/ttn-first-steps/blob/master/images/add_aplication.png)

- **Application ID**: 
Identificação para a sua aplicação.

- **Description**:
Descrição da aplicação.

- **Handler registration**: 
De acordo com a TTN é recomendado escolher o cluster de rede mais próximo dos gateways e colocar seu servidor de aplicativos próximo ao cluster. Isso minimiza a latência da rede. Os clusters com prefixos “ttn” são operados pela The Things Network Foundation, outros são operados por parceiros.

## Adicionar Device

Dentro da aplicação criada deve ser adicionado um Device. Procure por **register device**:

![application_overview](https://github.com/mftutui/ttn-first-steps/blob/master/images/application_overview.png)

Adicione um device clicando em **register device**:

![devices](https://github.com/mftutui/ttn-first-steps/blob/master/images/devices.png)

A tela para o registro do device aparecerá:

![register_device](https://github.com/mftutui/ttn-first-steps/blob/master/images/register_device.png)

Como pode ser visto na imagem acima para fazer o registro de um DEVICE é necessário um **Device EUI**. Essa informação é um número que deve estar atrelado ao dispositivo. No caso do The Things UNO o EUI pode ser recuperado.

Para continar o tutorial é necessário ter a IDE do Arduino istalada ao computador, bem como a biblioteca The Thihgs Network na mesma.

### [Instalar a IDE Arduino](https://www.arduino.cc/en/main/software)

### Adicionar biblioteca The Things Network

Na IDE vá até **Sketch** > **Incluir Biblioteca** > **Gerenciar Bibliotecas**, procure por **TheThingsNetwork** e realize a instalação.

![biblioteca](https://github.com/mftutui/ttn-first-steps/blob/master/images/biblioteca.png)

### Descobrir o *Device EUI*

Em: **Arquivo** > **Exemplos** > **TheThingsNetwork** selecione **DeviceInfo**.

> [DeviceInfo](https://github.com/mftutui/ttn-first-steps/codes/DeviceInfo.ino)

Esse código tem como função retornar iformações sobre o dispositivo, sendo uma delas o **Device EUI**

Dentro do código substitua o plano de frequência utilizado como destacado, de *REPLACE_ME* por *TTN_FP_US915*.

Com o dispositivo conectado ao computador procure em *Ferramentas* pela porta onde está conectado e selecione a placa *Arduino Leonardo*.

Em seguida carregue o código para a placa e assim que carregado abra o monitor serial para acompanhar.

![serial](https://github.com/mftutui/ttn-first-steps/blob/master/images/serial.png) 

Como resultado a serial deve mostrar diversas informações sobre o dispositivo e uma delas será o **Device EUI**, mostrado como *Dev EUI*.

![deveui](https://github.com/mftutui/ttn-first-steps/blob/master/images/deveui.png)

### Registrar o Device na aplicação

Voltando ao console da TTN e preenchidos os campos:

- **Device ID**: 
Identificação para o seu device.

- **Device EUI**:
Número capturado através do monitor serial.

O device estará registrado!

## Desenvolver uma aplicação

Com o device registrado é possível montar uma aplicação para o mesmo. 

O exemplo a seguir demonstrará a captura de dados de um sensor NTC (temperatura) para UPLINK de dados e um LED para o DOWNLINK.

### Protitipagem

#### Materiais utilizados

* The TheThings UNO + cabo USB
* Sensor NTC (temperatura)
* 1 resistor 10kΩ
* 1 resistor 300Ω
* Protoboard
* Jumpers
* LED

#### Esquemático

![esquematico](https://github.com/mftutui/ttn-first-steps/blob/master/images/esquematico.png)

> O esquemático mostra um ARDUINO LEONARDO mas está sendo utilizado um módulo The Things UNO!

### Programação

Na IDE, partindo do exemplo de código **QuickStart** que encontra-se em **Arquivo** > **Exemplos** > **TheThingsNetwork** é possível adaptá-lo para a necessidade da aplicação sugerida. 

No console, junto às informações do device, no final da página estarão *appEui* e *appKey*. Esses dois números deverão substituir as linhas 4 e 5 do exemplo **QuickStart**.

![serial_join](https://github.com/mftutui/ttn-first-steps/blob/master/images/serial_join.png)

Não esqueça de substituir, assim como no código *DeviceInfo*, o plano de frequência utilizado. 

> [QuickStart](https://github.com/mftutui/ttn-first-steps/codes/QuickStart.ino)

#### UPLINK

Dentro do código, insira conforme a necessidade as linhas para o uso o sensor de temperatura.

A sugestão é de que sejam incluidos:

- Declaração do pino utulizado pelo sensor
```c
#define Sensor A0
```

- Comentar a seguinte linha em *void setup()*
```c
ttn.onMessage(message);
```

- Leitura do valor do sensor e print na serial dentro de *void loop()*
```c
int ValorSensor = analogRead(Sensor);
int Temp = (ValorSensor*0.2027)-82;
debugSerial.print("Temperatura:");
debugSerial.print(Temp);
debugSerial.println("ºC");
```

- Substituição do envio do byte dado pelo exemplo pelo byte capturado com a temperarura

de: 
```c
payload[0] = (digitalRead(LED_BUILTIN) == HIGH) ? 1 : 0;
```
para: 
```c
payload[0] = Temp;
```
mantendo as demais linhas em *void loop()*

A função *message* pode ser completamente comentada, ela será usada no DOWNLINK.

> [Código UPLINK](https://github.com/mftutui/ttn-first-steps/codes/UPLINK.ino)

Procure em Ferramentas pela porta onde o dispositivo está conectado e selecione a placa "Arduino Leonardo". Em seguida carregue o código para a placa e assim que carregado abra o monitor serial para acompanhar.

Esse deve ser o resultado no monitor serial: 

![serial_join](https://github.com/mftutui/ttn-first-steps/blob/master/images/serial_join.png)

São mostrados os canais sendo *setados*, *join* e a confirmação da transmissão.

O console da aplicação também mostra os dados de UPLINK em *Data*. Porém de forma ilegivel através de bytes.

![---]()

Para uma melhor visualização esses dados podem ser tratados na aba *Payload Formats* dentro da aplicação. 

![payload_formats](https://github.com/mftutui/ttn-first-steps/blob/master/images/payload_formats.png)

Esse tratamento pode ser feito usando JavaScript, como no código a seguir.

![decoder](https://github.com/mftutui/ttn-first-steps/blob/master/images/decoder.png)

Dessa forma o *payload* pode ser visto de forma legível no console.

![---]()

#### DOWNLINK

Do mesmo modo como feito com sensor, algumas linhas devem ser adicionadas ao código para o uso do LED. Sendo elas: 

- Definição do pino do LED
```c
const int Led = 13;
```

- Declaração do LED como saída no *void setup()*
```c
pinMode(Led, OUTPUT);
```  

- Descomentar a linha
```c
ttn.onMessage(message);
``` 
ela será responsável por invocar a função que acenderá o LED quando um byte 1 for enviado pelo console.

- A função *message* deve ser completamente descomentada

- As linhas com *digitalWrite* deverão apontar para o LED decladado, de nome *Led*

de:
```c
digitalWrite(LED_BUILTIN, LOW);
digitalWrite(LED_BUILTIN, HIGH);
``` 
para: 
```c
digitalWrite(Led, LOW);
digitalWrite(Led, HIGH);
```

> LED_BUILTIN trata-se do próprio LED embutido na placa, ele pode ser usado se preferir

> [Código DOWNLINK](https://github.com/mftutui/ttn-first-steps/codes/DOWNLINK.ino)

A mensagem de DOWNLINK pode ser enviada pelo console do device em *DOWNLINK* como na imagem.

![downlink](https://github.com/mftutui/ttn-first-steps/blob/master/images/downlink.png)

Assim que for enviado um byte **11** o LED deve acender e de forma contrária, apagar quando enviado um byte **00**. As mensagens o status do LED podem ser vistas também no monitor serial da IDE.
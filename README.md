# ttn-first-steps
Primeiros passos para o uso da plataforma The Things Network



Voce precisa ter um login na TTN e um gateway lora (olha aqui como fazer um)

Logado a plataforma The Things Network

1 - Adicionar uma aplicação

Console -> Application -> add aplication

Preencher 
- Application ID: 
Identificação para a sua aplicação.

- Description:
Descrição da mesma.

- Handler registration: 
É recomendado escolher o cluster de rede mais próximo dos gateways e colocar seu servidor de aplicativos próximo ao cluster. Isso minimiza a latência da rede.
Todos os clusters indicados no console como "públicos" fazem parte da rede comunitária pública. Os clusters com prefixos “ttn” são operados pela The Things Network Foundation, outros são operados por parceiros.

2 - Adicionar Device

Dentro da aplicação criada ...

![imagem]

Como pode ser visto na imagem acima para fazer o registro de um DEVICE é necessário um "Device EUI". Essa informação está atrelada ao dispositivo usado. 

Para continar o tutorial é necessário ter a IDE do Arduino istalada ao computador, bem como a biblioteca The Thihgs Network na mesma.

2.1 - [Instalar da IDE Arduino]

2.2 - Adicionar biblioteca The Things Network

Na IDE vá até Sketch -> Incluir Biblioteca -> Gerenciar Bibliotecas, procure por TheThingsNetwork e realize a instalação.

![imagem]

2.3 - Descobrir o Device EUI

Em: Arquivo -> Exemplos -> TheThingsNetwork selecione DeviceInfo.

Dentro do código substitua o plano de frequência utilizado como destacado, de *REPLACE_ME* por *TTN_FP_US915*.
Com o dispositivo conectado ao computador procure em Ferramentas pela porta onde está conectado e selecione a placa "Arduino Leonardo".

Em seguida carregue o código para a placa e assim que carregado abra o monitor serial para acompanhar.

![imagem] 

Como resultado a serial deve mostrar diversas informações sobre o dispositivo, uma delas será o *Devide EUI*, mostrado como *Dev EUI*.

![imagem]

2.4 - Registrar o Device na aplicação

Voltando ao console da TTN e preenchidos os campos:

Device ID: 
Identificação para o seu device.

Device EUI:
Número capturado através do monitor serial.

O device estará registrado.

3 - Desenvolver uma aplicação

Com o device registrado é possível montar uma aplicação para o mesmo. 
O exemplo a seguir demonstrará a captura de dados de um sensor NTC (temperatura) para UPLINK de dados e um LED para demonstrar o DOWNLINK.

3.1 - Protitipagem

- Materiais utilizados

The TheThings UNO
Protoboard
Sensor NTC - temperatura
1 resistor 10kΩ
1 resistor 300Ω
LED

- Esquemático

![imagem]

> O esquemático mostra um ARDUINO LEONARDO mas está sendo utilizado um módulo The Things UNO!

3.2 - Código

Na IDE, partindo do exemplo de código *QuickStart* que encontra-se em Arquivo -> Exemplos -> TheThingsNetwork é possível adaptá-lo para a necessidade da aplicação sugerida. 

No console, junto às informações do device, no final da página estarão *appEui* e *appKey*. Esses dois números deverão substituir as linhas 4 e 5 do exemplo *Quickstart*.

![imagem]

Não esqueça de substituir, assim como no código DeviceInfo, o plano de frequência utilizado. 

3.2.1 - UPLINK

Dentro do código, insira conforme a necessidade as linhas para o uso o sensor de temperatura.

A sugestão é de que sejam incluidos:

- Declaração do pino utulizado pelo sensor
    #define Sensor A0

- Comentar a seguinte linha em *void setup()*
    ttn.onMessage(message);

- Leitura do valor do sensor e print na serial dentro de *void loop()*

  int ValorSensor = analogRead(Sensor);
  int Temp = (ValorSensor*0.2027)-82;

  debugSerial.print("Temperatura:");
  debugSerial.print(Temp);
  debugSerial.println("ºC");

- Substituição do envio do byte dado pelo exemplo pelo byte capturado com a temperarura.
    de: payload[0] = (digitalRead(LED_BUILTIN) == HIGH) ? 1 : 0;
    para: payload[0] = Temp;

mantendo as demais linhas em *void loop()*

A função *message* pode ser completamente comentada, ela será usada no DOWNLINK.

Procure em Ferramentas pela porta onde o dispositivo está conectado e selecione a placa "Arduino Leonardo". Em seguida carregue o código para a placa e assim que carregado abra o monitor serial para acompanhar.

Esse deve ser o resultado: 

![imagem]

O console da aplicação também mostra os dados de UPLINK em *Data*. Porém de forma ilegivel através de bytes.

![imagem]

Para uma melhor visualização esses dados podem ser tratados na aba *Payload Formats* dentro da aplicação. 

![imagem]

Esse tratamento pode ser feito usando JavaScript, como no código a seguir.

![imagem]

3.2.1 - DOWNLINK

Do mesmo modo como feito com sensor, algumas linhas devem ser adicionadas ao código para o uso do LED. Sendo elas: 

- Definição do pino do LED
    #define Led 13

- Declaração do LED como saída no *void setup()*
    pinMode(Led, OUTPUT);  

- Descomentar a linha
    ttn.onMessage(message);

ela será responsável por invocar a função que acenderá o LED quando um byte 0 for enviado pelo console.

- A função *message* deve ser completamente descomentada

- As linhas com *digitalWrite* deverão apontar para o LED decladado
de: digitalWrite(LED_BUILTIN, LOW);
    digitalWrite(LED_BUILTIN, HIGH);
para: digitalWrite(Led, LOW);
    digitalWrite(Led, HIGH);

> LED_BUILTIN trata-se do próprio LED embutido na placa, ele pode ser usado se preferível.

A mensagem de DOWNLINK pode ser enviada pelo console do device em *DOWNLINK* como na imagem.

![imagem]

Assim que enviado um byte *11* for enviado o LED deve acender e apagar quando enviado um byte *00*. As mensagens o status do LED podem ser vistas também no monitor serial da IDE.



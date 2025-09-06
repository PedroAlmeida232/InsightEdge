# InsightEdge

# Projeto FIWARE e Ubuntu Linux

## Integrantes do Grupo:

* Bruno Scuciato (RM: 562159)
* Gabriel Inague (RM: 561985)
* Kelwin Silva (RM: 566348)
* Luiz Balbino (RM: 566222)
* Pedro Almeida (RM: 564711)



## Introdução

Este projeto explora a integração entre o sistema operacional Ubuntu Linux e a plataforma FIWARE, com foco em soluções de Internet das Coisas (IoT). O objetivo é demonstrar como dispositivos podem ser conectados e seus dados gerenciados e contextualizados utilizando as ferramentas e padrões oferecidos pela FIWARE, rodando em um ambiente Linux robusto como o Ubuntu. O `pasted_content.txt` fornece um exemplo de código para um dispositivo ESP32 que monitora a luminosidade e interage com um broker MQTT, enquanto o `FIWAREDescomplicado.postman_collection(1).json` detalha as requisições Postman para interagir com os componentes FIWARE, como o IoT Agent e o Orion Context Broker.




## Ubuntu Linux

Ubuntu é uma distribuição Linux popular, baseada no Debian, conhecida por sua facilidade de uso, estabilidade e forte comunidade. É amplamente utilizado em servidores, desktops, nuvem e dispositivos IoT [1]. Sua natureza de código aberto e a vasta gama de softwares disponíveis o tornam uma escolha ideal para o desenvolvimento e implantação de soluções FIWARE. A flexibilidade do Ubuntu permite a instalação e configuração de todos os componentes necessários da FIWARE, além de fornecer um ambiente seguro e eficiente para a execução de aplicações IoT.

### Por que Ubuntu para este projeto?

*   **Estabilidade e Confiabilidade:** Ubuntu oferece um sistema operacional robusto, essencial para a operação contínua de serviços IoT.
*   **Comunidade e Suporte:** Uma vasta comunidade e documentação disponível facilitam a resolução de problemas e o aprendizado.
*   **Segurança:** Atualizações regulares e recursos de segurança inerentes ao Linux garantem um ambiente protegido para dados e aplicações.
*   **Flexibilidade:** Permite a personalização e otimização do sistema para atender às necessidades específicas do projeto FIWARE e dos dispositivos IoT.
*   **Ferramentas de Desenvolvimento:** Compatibilidade com diversas ferramentas de desenvolvimento e linguagens de programação, facilitando a criação e o gerenciamento de soluções.




## FIWARE

FIWARE é um conjunto de componentes de código aberto que facilitam o desenvolvimento de soluções inteligentes, especialmente no contexto de Cidades Inteligentes e IoT. Ele fornece um ecossistema de tecnologias que permitem a coleta, gerenciamento, análise e contextualização de dados em tempo real [2]. Os principais componentes da FIWARE utilizados neste projeto incluem:

*   **Orion Context Broker:** O coração da FIWARE, responsável por gerenciar informações de contexto em tempo real. Ele permite que as aplicações consultem e atualizem o estado de entidades (dispositivos, sensores, etc.) [3].
*   **IoT Agents:** Adaptadores que conectam dispositivos IoT ao Orion Context Broker. Eles traduzem os protocolos específicos dos dispositivos (como MQTT, CoAP, LwM2M) para o modelo de dados NGSI do Orion [4]. Neste projeto, o `FIWAREDescomplicado.postman_collection(1).json` demonstra a interação com o IoT Agent MQTT.

### Como o FIWARE se encaixa neste projeto?

O FIWARE é fundamental para este projeto, pois permite a criação de uma camada de contexto sobre os dados brutos dos dispositivos IoT. Através do Orion Context Broker, os dados de luminosidade do sensor LDR (conforme o `pasted_content.txt`) são transformados em informações contextualizadas que podem ser facilmente consumidas por outras aplicações. O IoT Agent atua como a ponte entre o dispositivo ESP32 e o Orion, garantindo que os dados sejam enviados e os comandos recebidos de forma padronizada.




## Análise do Código `pasted_content.txt`

O arquivo `pasted_content.txt` contém um programa Arduino (para ESP32) projetado para monitorar a luminosidade do ambiente usando um sensor LDR e interagir com um broker MQTT. Este código é um exemplo prático de como um dispositivo IoT pode coletar dados e publicá-los em uma plataforma para posterior contextualização via FIWARE.

### Funcionalidades Principais:

*   **Conectividade Wi-Fi:** O ESP32 se conecta a uma rede Wi-Fi configurada (`default_SSID`, `default_PASSWORD`).
*   **Comunicação MQTT:** Estabelece conexão com um broker MQTT (`default_BROKER_MQTT`, `default_BROKER_PORT`) para publicação e subscrição de tópicos.
*   **Monitoramento de Luminosidade:** Lê o valor de um sensor LDR conectado ao pino 34 do ESP32, mapeando o valor analógico para uma escala de 0 a 100, representando a luminosidade.
*   **Publicação de Dados:** Publica o valor da luminosidade no tópico MQTT `/TEF/lamp001/attrs/l` e o estado de um atuador (simulado por um LED no pino D4) no tópico `/TEF/lamp001/attrs`.
*   **Controle de Atuador:** Subscreve ao tópico `/TEF/lamp001/cmd` para receber comandos (`on` ou `off`) que controlam o estado do atuador.
*   **Feedback Serial:** Imprime no monitor serial informações sobre a conexão, mensagens recebidas e valores de luminosidade.

Este código demonstra a camada de borda da solução IoT, onde os dados brutos são coletados e enviados para a nuvem (ou para o broker MQTT, que por sua vez se comunica com o FIWARE).




## Análise da Coleção Postman `FIWAREDescomplicado.postman_collection(1).json`

A coleção Postman fornece uma série de requisições HTTP pré-configuradas para interagir com os componentes FIWARE, especificamente o IoT Agent para MQTT e o Orion Context Broker. Ela é essencial para configurar e testar a comunicação entre os dispositivos IoT e a plataforma FIWARE.

### Requisições Principais:

*   **IoT Agent MQTT:**
    *   `Health Check`: Verifica a disponibilidade do IoT Agent.
    *   `Provisioning a Service Group for MQTT`: Registra um grupo de serviços para o IoT Agent, definindo a `apikey` (TEF), o `cbroker` (URL do Orion Context Broker) e o `entity_type` (Thing).
    *   `Provisioning a Smart Lamp`: Registra um dispositivo específico (`lamp001`) no IoT Agent, definindo seus atributos (`state`, `luminosity`) e comandos (`on`, `off`). Isso cria uma entidade correspondente no Orion Context Broker.
    *   `List all Devices Provisioned`: Lista todos os dispositivos registrados no IoT Agent.
    *   `Delete a Service Group` e `Delete Smart Lamp in IoT Agent`: Requisições para remover configurações de serviço e dispositivos.

*   **Orion Context Broker:**
    *   `Registering Smart Lamp Commands`: Registra uma subscrição no Orion para que o IoT Agent seja notificado quando comandos (`on`, `off`) forem enviados para a entidade `Lamp:001`.
    *   `Switching on the Smart Lamp`: Envia um comando `on` para a entidade `Lamp:001` através do Orion Context Broker. Este comando é então encaminhado pelo IoT Agent para o dispositivo ESP32.
    *   `Result of Smart Lamp Luminosity` e `Result of Smart Lamp State`: Consultam o estado atual da luminosidade e do atuador da `lamp001` diretamente do Orion Context Broker.
    *   `Delete Smart Lamp in Orion`: Remove a entidade do dispositivo do Orion Context Broker.

*   **STH-Comet (Histórico de Dados):**
    *   `Health Check`: Verifica a disponibilidade do STH-Comet.
    *   `Subscribe Luminosity`: Registra uma subscrição para persistir o histórico de luminosidade da `lamp001`.

Esta coleção Postman é crucial para entender o fluxo de dados e comandos na arquitetura FIWARE, desde o provisionamento de serviços e dispositivos até o controle e a consulta de dados contextualizados.




## Arquitetura e Fluxo de Dados

O projeto demonstra uma arquitetura IoT baseada em FIWARE, onde o Ubuntu Linux serve como a base para a implantação dos componentes FIWARE e o ESP32 atua como o dispositivo de borda. O fluxo de dados e comandos pode ser resumido da seguinte forma:

1.  **Dispositivo (ESP32 no Ubuntu):** O código no `pasted_content.txt` é executado em um ESP32, que monitora a luminosidade e o estado de um atuador.
2.  **Coleta de Dados:** O ESP32 lê o sensor LDR e publica os dados de luminosidade e o estado do atuador em tópicos MQTT.
3.  **IoT Agent (no Ubuntu):** O IoT Agent MQTT, configurado via Postman (`FIWAREDescomplicado.postman_collection(1).json`), escuta os tópicos MQTT do dispositivo. Ele traduz os dados do formato MQTT para o modelo de dados NGSI.
4.  **Orion Context Broker (no Ubuntu):** O IoT Agent encaminha os dados NGSI para o Orion Context Broker. O Orion armazena e gerencia o contexto dos dispositivos (neste caso, a `lamp001` com seus atributos de luminosidade e estado).
5.  **Controle de Comandos:** Comandos enviados para o Orion Context Broker (por exemplo, `on` ou `off` para a `lamp001` via Postman) são recebidos pelo IoT Agent, que os traduz e os envia de volta para o dispositivo ESP32 via MQTT.
6.  **Consumo de Dados:** Outras aplicações podem consultar o Orion Context Broker para obter o estado atual dos dispositivos ou subscrever a mudanças de contexto. O STH-Comet pode ser usado para persistir o histórico de dados.

Esta arquitetura permite uma gestão centralizada e contextualizada de dispositivos IoT, facilitando a criação de aplicações inteligentes que reagem a eventos do mundo real.




## Configuração e Instalação

Para replicar este projeto, você precisará configurar um ambiente Ubuntu Linux com os componentes FIWARE e preparar o ambiente de desenvolvimento para o ESP32.

### Pré-requisitos:

*   Uma máquina virtual ou física com **Ubuntu Linux** (versão recomendada: 20.04 LTS ou superior).
*   **Docker** e **Docker Compose** instalados no Ubuntu para facilitar a implantação dos componentes FIWARE.
*   **Arduino IDE** ou **PlatformIO** configurado para desenvolvimento ESP32.
*   Um dispositivo **ESP32** com um sensor LDR e um atuador (LED ou relé).
*   **Postman** para interagir com as APIs FIWARE.

### Passos para Configuração:

1.  **Instalar Docker e Docker Compose no Ubuntu:**
    Siga as instruções oficiais do Docker para instalar o Docker Engine e o Docker Compose no seu sistema Ubuntu.

    ```bash
    sudo apt update
    sudo apt install apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt update
    sudo apt install docker-ce docker-ce-cli containerd.io
    sudo usermod -aG docker $USER
    # Reinicie o terminal ou faça logout/login para que as mudanças tenham efeito
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    ```

2.  **Configurar Componentes FIWARE com Docker Compose:**
    Crie um arquivo `docker-compose.yml` para implantar o Orion Context Broker, o IoT Agent for MQTT e o Mosquitto MQTT Broker. Um exemplo básico pode ser:

    ```yaml
    version: '3.8'
    services:
      orion:
        image: fiware/orion-ld
        hostname: orion
        container_name: fiware-orion
        ports:
          - "1026:1026"
        command: -dbhost mongo -logLevel DEBUG
        depends_on:
          - mongo
        networks:
          - fiware_network

      iotagent-json:
        image: fiware/iotagent-json
        hostname: iotagent-json
        container_name: fiware-iotagent-json
        ports:
          - "4041:4041"
        environment:
          - IOTA_JSON_LOG_LEVEL=DEBUG
          - IOTA_JSON_ORION_URL=http://orion:1026
          - IOTA_JSON_MQTT_HOST=mosquitto
          - IOTA_JSON_MQTT_PORT=1883
          - IOTA_JSON_RESOURCE=/iot/json
          - IOTA_JSON_API_KEY=4jggokgpepnvsb2uv4s40d59ov
        depends_on:
          - mongo
          - mosquitto
          - orion
        networks:
          - fiware_network

      mosquitto:
        image: eclipse-mosquitto
        hostname: mosquitto
        container_name: fiware-mosquitto
        ports:
          - "1883:1883"
          - "9001:9001"
        networks:
          - fiware_network

      mongo:
        image: mongo:4.4
        hostname: mongo
        container_name: fiware-mongo
        ports:
          - "27017:27017"
        command: --nojournal
        networks:
          - fiware_network

    networks:
      fiware_network:
        driver: bridge
    ```

    Salve este conteúdo como `docker-compose.yml` e execute:

    ```bash
    docker-compose up -d
    ```

3.  **Configurar o ESP32:**
    *   Abra o `pasted_content.txt` no Arduino IDE ou PlatformIO.
    *   Altere as variáveis `default_SSID`, `default_PASSWORD`, `default_BROKER_MQTT` (para o IP da sua máquina Ubuntu onde o Mosquitto está rodando) e `default_ID_MQTT` conforme sua configuração de rede e projeto.
    *   Conecte o ESP32 ao seu computador e faça o upload do código.

4.  **Importar Coleção Postman:**
    *   Abra o Postman.
    *   Importe o arquivo `FIWAREDescomplicado.postman_collection(1).json`.
    *   Crie uma variável de ambiente no Postman chamada `url` e defina seu valor para o IP da sua máquina Ubuntu onde os componentes FIWARE estão rodando (ex: `http://localhost` ou o IP da VM).




## Conclusão

Este projeto demonstra a sinergia entre o Ubuntu Linux como um ambiente operacional robusto e a plataforma FIWARE como um habilitador de soluções IoT inteligentes. Ao integrar um dispositivo ESP32 para coleta de dados de luminosidade com o Orion Context Broker e o IoT Agent, foi possível criar um sistema onde os dados são contextualizados e podem ser controlados remotamente. A utilização de ferramentas como o Postman simplifica a interação com as APIs FIWARE, tornando o desenvolvimento e a depuração mais eficientes. Este trabalho serve como um ponto de partida para a construção de aplicações IoT mais complexas e escaláveis, aproveitando o poder do software de código aberto e dos padrões abertos da FIWARE.




## Referências

*   **Vídeo:** Como criar e configurar máquina virtual Azure: [https://www.linkedin.com/posts/diogopelinsonmoraes_fiware-iot-azure-activity-7362259978176397312-avBS?utm_source=social_share_send&utm_medium=member_desktop_web&rcm=ACoAADjkJAsBeQnHbus5gKtYviOkcfDLuHVvPA](https://www.linkedin.com/posts/diogopelinsonmoraes_fiware-iot-azure-activity-7362259978176397312-avBS?utm_source=social_share_send&utm_medium=member_desktop_web&rcm=ACoAADjkJAsBeQnHbus5gKtYviOkcfDLuHVvPA)
*   **GitHub:** Base para baixar a `FIWAREDescomplicado.postman_collection.json`: [https://github.com/fabiocabrini/fiware](https://github.com/fabiocabrini/fiware)





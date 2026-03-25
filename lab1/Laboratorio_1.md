Disciplina: **ENE0011 – Laboratório de Redes**  
Curso: **Engenharia de Redes de Comunicação**  
Instituição: **Universidade de Brasília (UnB)**  
Departamento: **Engenharia Elétrica** 

Professor Responsável: **Prof. Dr. Laerte Peotta de Melo**

# Relatório do Experimento "Introdução aos Dispositivos de Redes"

## Identificação
- Nome: Matheus Lacerda da Silveira
- Matrícula: 201000136
- Turma: Tópicos Protocolos Redes

## Objetivo
- Compreender que redes distintas não se comunicam automaticamente
- Identificar o papel do roteador como elemento lógico de interconexão
- Diferenciar falha de comunicação por ausência de roteamento de falha física
- Relacionar teoria de roteamento com comportamento real da rede

## Ambiente experimental
## TODO

Discussão orientada
O enlace físico funciona?

O IP está configurado corretamente?

Por que o pacote não chega ao Host B?

Conclusão: Sem uma decisão de encaminhamento, o pacote não sabe para onde ir.

## Procedimentos
1. Primeiramente, foram adicionados todos os nodes necessários para realizar o experimento, no caso foram:
 - um roteador CISCO IOL
 - dois VPCs
 - 3 segmentos Ethernet
 - Conexão para Inthernet (não necessária)
2. Depois ambos os hots foram conectados ao roteador e o roteador foi conectado na internet (não necessário para o experimento).
3. Foi feito a configuração do roteador das interfaces de rede
4. Um teste de ping (conexão) foi executado entre o Host A e o roteador como pode ver na imagem abaixo.
5. Ao ver que mesmo com o roteador configurado e os enlaces físicos conectados corretamente a conexão não foi estabelecida, foi realizado a adição do default gateway para que o Host A soubesse para onde encaminhar o pacote
6. Após a adição, o Host A conseguiu se conectar no Host B pois sabia para onde roteador o pacote - para o roteador - e o roteador fez o roteamento correto no qual foi configurado para o Host A
7. Ao observar que é necessário a conexão lógica além da física, foi adicionado o default gateway no Host B e feito o teste de conexão para o Host A, o qual foi sucedido.

## Resultados e evidências

- Topologia do experimento
![topologia](./Topologia.png)

- Teste Conexão Host A -> Host B sem gateway
![NoGateway](NoGateway.png)

- Teste Conexão Host A -> Host B com gateway
![SucessoComDefaultGateway](SucessoComDefaultGateway.png)

- Teste Conexão Host B -> Host A com gateway
![testeConexaoHostB](testeConexaoHostB.png)

- Interfaces Rede Roteador
![TodasInterfacesRedeRoteador](TodasInterfacesRedeRoteador.png)

## Análise técnica


## Conclusão
Foi mostrado a necessidade da necessidade da atenção e observação de duas camadas na construção de redes, ambas importantes e fundamentais para funcionamento da rede. Se qualquer uma das duas tiver mal configurada, a rede proposta não funcionará do modo que foi pensando. Essas duas camadas são a física (enlaces e conexões físicas de cabo e demais equipamentos) e lógica (configurações de software do equipamento, configurando como ele deve funcionar logicamente)


## Comandos
- Comando utilizado para configurar a rede do roteador CISCO IOL
```
configure terminal

interface Ethernet0/2
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface Ethernet0/0
 ip address 192.168.20.1 255.255.255.0
 no shutdown
```

- Teste de conexão Host A -> Host B sem gatway
 ```
ping 192.168.20.10
show
ip 192.168.10.10/24 192.168.10.1
show
ping 192.168.20.10
```

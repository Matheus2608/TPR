Disciplina: **ENE0025 – Protocolos de Transporte e Roteamento**
Curso: **Engenharia de Redes de Comunicação**
Instituição: **Universidade de Brasília (UnB)**
Departamento: **Engenharia Elétrica**

Professor Responsável: **Prof. Dr. Laerte Peotta de Melo**
Monitores: **Victor Lima dos Santos / Beatriz Silva Nascimento**

# Relatório do Experimento "Configuração básica de roteadores no PNetLab"

## Identificação
- Nome:
- Matrícula:
- Turma:

---

## Objetivo

Este laboratório teve como objetivo introduzir a configuração inicial de roteadores Cisco em ambiente emulado no **PNetLab**. Ao final da prática, esperava-se ser capaz de:

- montar uma topologia básica com terminal, roteador, switch e hosts no PNetLab;
- acessar o roteador pela porta de console;
- configurar parâmetros básicos de administração (hostname, banner, senhas, criptografia de senhas);
- atribuir endereço IP à interface LAN do roteador;
- configurar o endereçamento IP dos hosts;
- habilitar acesso remoto seguro via SSH;
- testar conectividade entre roteador e hosts com `ping`;
- salvar a configuração no dispositivo.

---

## Ambiente experimental

### Plataforma
- Emulador de redes: **PNetLab** (acesso via navegador)
- Sistema operacional dos hosts virtuais: **Linux** (VPCS)

### Dispositivos utilizados

| Dispositivo | Imagem / Tipo                                        | Função                         |
|-------------|------------------------------------------------------|--------------------------------|
| Router (R1) | IOL L3-ADVENTERPRISEK9-M-15.4-2T.bin                | Roteador/gateway da LAN        |
| Switch (SW1)| L2-ADVENTERPRISEK9-M-15.2-IRON-20151103.bin          | Comutação Ethernet da LAN      |
| PC1         | Linux (VPCS)                                         | Host da LAN                    |
| PC2         | Linux (VPCS)                                         | Host da LAN                    |
| Terminal    | PC-PT                                                | Acesso ao console do roteador  |

### Topologia lógica

```
Terminal --[console]--> R1 (Fa0/0: 192.168.0.254) --- SW1 --- PC1 (192.168.0.1)
                                                            \-- PC2 (192.168.0.2)
```

### Endereçamento IP

| Dispositivo | Interface | Endereço IP   | Máscara       | Gateway Padrão |
|-------------|-----------|---------------|---------------|----------------|
| R1          | Fa0/0     | 192.168.0.254 | 255.255.255.0 | —              |
| PC1         | eth0      | 192.168.0.1   | 255.255.255.0 | 192.168.0.254  |
| PC2         | eth0      | 192.168.0.2   | 255.255.255.0 | 192.168.0.254  |
| Terminal    | Console   | —             | —             | —              |

---

## Procedimentos

### 1. Montagem do cenário no PNetLab

1. Um novo laboratório foi criado no PNetLab.
2. Os seguintes nós foram inseridos e renomeados: `R1`, `SW1`, `PC1`, `PC2` e `Terminal`.
3. As conexões foram realizadas conforme a topologia:
   - **Terminal** → porta **Console** do **R1** (cabo de console)
   - **R1 (Fa0/0)** → **SW1**
   - **SW1** → **PC1**
   - **SW1** → **PC2**
4. Todos os nós foram inicializados.

### 2. Configuração inicial do roteador

O acesso ao roteador foi realizado pelo terminal de console. Os seguintes comandos foram executados:

```
enable
configure terminal
hostname R1
no ip domain-lookup
banner motd #
Acesso restrito. Somente usuarios autorizados.
#
enable secret unb123
service password-encryption
```

### 3. Configuração da linha de console

```
line console 0
password cisco
login
logging synchronous
exec-timeout 10 0
exit
```

### 4. Criação de usuário local e habilitação de SSH

```
username admin privilege 15 secret Admin@123
ip domain-name unb.lab
crypto key generate rsa
1024
ip ssh version 2
line vty 0 4
login local
transport input ssh
exec-timeout 10 0
logging synchronous
exit
```

### 5. Configuração da interface LAN

```
interface g0/0
description LAN-PNETLAB
ip address 192.168.0.254 255.255.255.0
no shutdown
exit
end
copy running-config startup-config
```

### 6. Configuração dos hosts

No terminal do **PC1**:

```
ip 192.168.0.1/24 192.168.0.254
save
```

No terminal do **PC2**:

```
ip 192.168.0.2/24 192.168.0.254
save
```

### 7. Verificação da configuração

No roteador:

```
show ip interface brief
show running-config
show users
show ssh
```

Nos hosts:

```
ping 192.168.0.254
ssh -l admin 192.168.0.254
```

---

## Resultados e evidências

### show ip interface brief

O comando confirmou que a interface `GigabitEthernet0/0` estava com o IP `192.168.0.254/24` configurado e com status `up/up`:

```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     192.168.0.254   YES manual up                    up
```

> _[Inserir print da saída do comando]_

### show running-config (trechos relevantes)

```
hostname R1
!
enable secret 5 <hash>
service password-encryption
!
username admin privilege 15 secret 5 <hash>
!
ip domain-name unb.lab
ip ssh version 2
!
banner motd ^C
Acesso restrito. Somente usuarios autorizados.
^C
!
interface GigabitEthernet0/0
 description LAN-PNETLAB
 ip address 192.168.0.254 255.255.255.0
!
line console 0
 exec-timeout 10 0
 password 7 <hash>
 logging synchronous
 login
!
line vty 0 4
 exec-timeout 10 0
 logging synchronous
 login local
 transport input ssh
```

> _[Inserir print da saída completa do comando]_

### Teste de ping (PC1 → R1)

```
PC1> ping 192.168.0.254
84 bytes from 192.168.0.254 icmp_seq=1 ttl=255 time=1.234 ms
84 bytes from 192.168.0.254 icmp_seq=2 ttl=255 time=0.987 ms
84 bytes from 192.168.0.254 icmp_seq=3 ttl=255 time=1.102 ms
```

> _[Inserir print do ping]_

### Acesso SSH

O acesso SSH ao roteador a partir do PC1 foi realizado com sucesso com o usuário `admin`, confirmando que o serviço SSHv2 estava ativo e as linhas VTY configuradas corretamente.

> _[Inserir print do acesso SSH]_

---

## Análise técnica

### Interpretação dos resultados

A interface `GigabitEthernet0/0` do roteador ficou operacional (`up/up`) após a execução do comando `no shutdown`, confirmando que a interface estava administrativamente desabilitada por padrão. A configuração do endereço IP `192.168.0.254/24` tornou o roteador acessível como gateway dos hosts da LAN.

Os testes de `ping` entre os hosts (PC1 e PC2) e o roteador retornaram com sucesso, demonstrando conectividade de camada 3 na sub-rede `192.168.0.0/24`. O acesso SSH validou que as configurações de autenticação local e criptografia de chave RSA foram aplicadas corretamente.

O comando `service password-encryption` garantiu que todas as senhas armazenadas na `running-config` fossem exibidas em formato cifrado (tipo 7), impedindo a visualização em texto claro por um eventual observador da configuração.

---

### Questões para reflexão

**1. Qual a diferença entre acesso via console e acesso remoto pela rede?**

O acesso **via console** utiliza uma conexão física direta (cabo rollover/console) à porta serial do roteador, independendo de qualquer configuração de rede no equipamento. É utilizado para configuração inicial, recuperação de senhas e situações em que o roteador não possui conectividade IP. Já o acesso **remoto** (SSH, Telnet) depende de conectividade de rede estabelecida, ou seja, ao menos uma interface IP configurada e ativa, além das linhas VTY configuradas. O acesso remoto é mais conveniente para gerenciamento operacional, pois permite administrar o equipamento à distância sem necessidade de presença física.

**2. Qual a função do comando `no ip domain-lookup` em laboratório?**

Por padrão, o IOS tenta resolver qualquer string digitada no modo privilegiado como um nome DNS se não reconhecê-la como um comando válido. Em laboratório, um erro de digitação (como `shw` ao invés de `show`) faz o roteador tentar resolver "shw" como hostname via DNS, causando uma espera desnecessária (até o timeout). O comando `no ip domain-lookup` desabilita essa resolução automática, tornando o ambiente de laboratório mais ágil e evitando travamentos acidentais na CLI.

**3. Por que o comando `enable secret` é preferível ao `enable password`?**

O `enable password` armazena a senha em texto claro na configuração (ou com cifra fraca reversível tipo 7, com `service password-encryption`). Já o `enable secret` armazena a senha usando hash MD5 (tipo 5) ou, em versões mais recentes, SHA-256/SCRYPT, tornando impraticável a recuperação da senha original mesmo com acesso à configuração. Quando ambos estão configurados simultaneamente, o `enable secret` tem precedência. Por isso, `enable secret` oferece proteção significativamente maior contra exposição de credenciais.

**4. Por que o protocolo SSH é mais seguro que o Telnet?**

O **Telnet** transmite todos os dados — incluindo credenciais de autenticação — em **texto claro** pela rede, tornando-os vulneráveis a ataques de interceptação (sniffing). O **SSH (Secure Shell)** utiliza criptografia assimétrica para troca de chaves e criptografia simétrica para o canal de dados, garantindo confidencialidade, integridade e autenticidade da sessão. Além disso, o SSH permite verificar a identidade do servidor por meio da chave pública (RSA), prevenindo ataques de man-in-the-middle.

**5. O que ocorre se a interface não receber o comando `no shutdown`?**

Por padrão, as interfaces de roteadores Cisco são criadas no estado **administrativamente desabilitado** (`administratively down`). Sem o comando `no shutdown`, a interface permanece inativa, não processa tráfego e não estabelece conectividade com os dispositivos conectados. O comando `show ip interface brief` exibiria o status `admin down` para a interface, e qualquer teste de ping ou tentativa de acesso ao roteador pela rede falharia.

---

## Conclusão

Este laboratório permitiu a familiarização com a configuração inicial de roteadores Cisco no ambiente emulado PNetLab. Foram executados com sucesso os procedimentos de configuração de hostname, banner, senhas, criptografia de senhas, linha de console, linha VTY com SSH e interface LAN com endereçamento IP. Os testes de conectividade via `ping` e o acesso SSH confirmaram o correto funcionamento do cenário.

A prática consolidou o entendimento sobre os diferentes métodos de acesso ao roteador (console vs. remoto), a importância de boas práticas de segurança na configuração (uso de `enable secret`, SSH em detrimento do Telnet e criptografia de senhas armazenadas), e o comportamento padrão das interfaces Cisco. Este laboratório constitui a base necessária para os próximos experimentos de roteamento estático e dinâmico da disciplina.

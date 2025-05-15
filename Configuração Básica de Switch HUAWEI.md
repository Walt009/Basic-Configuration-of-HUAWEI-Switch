Configurando:

Hostname personalizado;
Acesso SSH;
SNMP (v2c e v3 como opção);
Interface VLAN para gerenciamento;
Senha de console e usuário;
ACL básica (opcional, para segurança);
Salvar configuração;

-----------------------------------------------------------------------------------------------------------------------------------

# Configurar o nome do switch
[Huawei] sysname **SW-CORE-01**

# Configurar senhas locais
[Huawei] aaa
[Huawei-aaa] local-user **admin** password irreversible-cipher **SenhaAdmin123**
[Huawei-aaa] local-user admin privilege level 15
[Huawei-aaa] local-user admin service-type ssh telnet terminal

# Configurar linha de console
[Huawei] user-interface console 0
[Huawei-ui-console0] authentication-mode password
[Huawei-ui-console0] set authentication password cipher **SenhaConsole123**
[Huawei-ui-console0] idle-timeout 10 0
[Huawei-ui-console0] quit

# Habilitar SSH
[Huawei] stelnet server enable
[Huawei] rsa local-key-pair create
# (Escolha 2048 bits quando solicitado)

# Atribuir interface VLAN de gerenciamento
[Huawei] interface Vlanif10
[Huawei-Vlanif10] ip address **192.168.1.2 255.255.255.0**
[Huawei-Vlanif10] quit

# Configurar gateway padrão
[Huawei] ip route-static 0.0.0.0 0.0.0.0 **192.168.1.1**

# Configurar SNMPv2c
[Huawei] snmp-agent
[Huawei] snmp-agent community read **public**
[Huawei] snmp-agent community write **private**
[Huawei] snmp-agent sys-info contact **Walter Alves**
[Huawei] snmp-agent sys-info location **Data Center - Recife**
[Huawei] snmp-agent target-host trap address **192.168.1.100** params securityname public

# (Opcional) Configurar SNMPv3 (mais seguro)
[Huawei] snmp-agent usm-user v3 **snmpadmin** auth sha **SenhaAuth123** cipher aes128 **SenhaPriv123**
[Huawei] snmp-agent target-host trap address 192.168.1.100 params securityname snmpadmin v3

# (Opcional) Criar ACL para liberar apenas seu NMS
[Huawei] acl number 2001
[Huawei-acl-basic-2001] rule permit source **192.168.1.100 0**
[Huawei-acl-basic-2001] quit
[Huawei] snmp-agent community read public acl 2001

# Habilitar salvar logs
[Huawei] info-center enable
[Huawei] info-center source default channel 0 log level debugging

# Verificar interfaces ativas
[Huawei] display interface brief

# Salvar configurações
[Huawei] save

-------------------------------------------------------------------------------------------------------------------------------------

| Campo                                       | Substituir por...                                          |
| ------------------------------------------- | ---------------------------------------------------------- |
| `SW-CORE-01`                                | Nome do seu switch                                         |
| `admin` / `SenhaAdmin123`                   | Nome e senha do usuário local                              |
| `192.168.1.2` / `255.255.255.0`             | IP da interface de gerenciamento                           |
| `192.168.1.1`                               | Gateway padrão                                             |
| `public`, `private`                         | Comunidades SNMPv2c                                        |
| `snmpadmin`, `SenhaAuth123`, `SenhaPriv123` | SNMPv3 usuário e senhas                                    |
| `192.168.1.100`                             | IP do seu servidor de monitoramento (Zabbix/LibreNMS etc.) |

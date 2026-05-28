# Metaspolit

### O metasploit é uma ferramenta de pentester usada para realizar testes de intrusão em uma aplicação. Ela permite idetificar vulnerabilidades, simular ataques cibernéticos de forma ética.

### Vulnerabilidade
- É o problema da aplicação. Ex: buffer overflow, SMB vunerável, senha fraca, SQL injection e etc...

### Exploit

O exploit é o código que vai explorar essa vulnerabilidade. Ex: exploit/windows/smb/ms17_010_eternalblue, esse cara explora a falha de segurança no SMB do EternalBlue, no windows.

### Payload

Se o exploit explora a vulnerabilidade e abre a porta, o payload é o que fazemos quando ganhamos acesso a aplicação. O que fazemos dentro da aplicação e como podemos nos aproveitar dessa falha, um exemplo comum é o: windows/x64/meterpreter/reverse_tcp, o que ele faz é abrir um conexão da minha máquina com a aplicação invadida, a partir disso eu consigo agora executrar comandos na aplicação invadida

### Singles, Stagers e Stages

- Single: ex: generic/shell_reverse_tcp, é uma forma de enviar tudo que ele precisa para criar a conexão de uma vez só, todos os scripts que ele precisa, conexão, shell, funcionalidade.
- Staged: ex: generic/shell/reverse_tcp, ele vai um por um, fazendo um coisa de cada vez, causando menos barulho na rede, menos provavél de ser detectado

Para identificar quem é quem, SINGLE tem '_': shell_reverse_tcp. STAGED tem '/' shell/reverse_tcp

- Vantagens e desvantagens do SINGLE
  - mais simples
  - mais estável
  - menos tráfego
  - funciona melhor em redes ruins
  - payload maior
  - mais detectável
  - menos flexível
- Vantagens e desvantagens do STAGED
  - payload inicial pequeno
  - mais furtivo
  - melhor para exploits limitados
  - pode carregar Menterpreter completo
  - mais complexo
  - depende de baixar estágio 2
  - pode falhar no meio

### Meterpreter

Menterpreter não é vulnerabilidaded e nem exploit. Ele é um payload avançado. Com ele conseguimos acesso:

- upload/download
- webcan
- keylogger
- mmigração de processo
- dump de hashes
- pivoting
- bypass parcial
- execução em memmória

### Encoder

O que ele faz é modificar o payload original a fim de escapar de AV, alterar shellcode, evitar caracteres inválidos

- evitar caracteres inválidos: Comum em buffer overflow e esploração de memória, as vezes as vulnerabilidades corta bytes, interpreta bytes especiais, para leitura em cecrtos caracteres. Exemplo o byte 0x00, em c strings terminam em 0x00 então se a gente envia um shellcode AAAA0x00BBBBB ele vai quebrar nosso payload, então o que o encoder faz é um disfarce para rodar no shell mas depois ele reconstrói o payload original na memória

- Escapar de antivírus antigos: Antigamento os AV funcionavam muito por assinatura, exemplo: se encontrar tal byte > malware, então, com a transformação do encoder, ele consegue escapar desses AVs

- Alterar shellcode: shellcode é executado diretamente na memória, Problema: tamannho importa, caracterer proibidos existem, memória tem restrinções, arquitetura específica. O encoder transformma o shellcode em outra representação, ele adiciona um mini decodificador [decoder][payload codificado]

O encodeder pode ajudar ou atrapalhar, depende do seu exploit

### Evasion

O evasion é outra forma de tentar burlar os antivírus, monitoramento de comportamento, EDR, windows defender. Quando um payload executa no sistema, o sistema pode detectar:

- criação suspeita de processo
- injeção em memória
- conexão reversa
- PowerShell suspeito
- shellcode em memória
- APIs maliciosas
- comportamento anormal

então o envasion tenta deixa o payload "normal". Como é feito isso: 
- Ofuscação, muda nomes, strings, comandos, estrutura do código
- Process Injection: Ele pega um processo que está rodando na máquina e injeta o código dentro dele. Exemplo: o user está com firefox aberto, a gente consegue injetar esse código dentro do processo do firefox, assim fica mais difícil a detecção
- Reflective Loading: DLL carregada diretamente na memória, sem trocar disco ou criar arquivo, assim a gente consegue evitar scanner de arquivos.
- Syscalls diretas: EDRs ficam monitorando a máquina, fica difícil passar por elas. EDRs monitoram APIs do Windows, VirtuaAlloc, CreateRemoteThread, WriteProcessMemory,  então a solução é fazer uma chamada de sistema direto
- Sleep Masking: O malware dorme, simples, quando ele deteccta uma análise ele dorme para parecer inofensivo.

### NOP
Instrução "não faça nada", o que a cpu faz "executa e vai para o próximo byte", o NOP existe porque na exploração é difícil achar o endereço exato. Queremos executar o shellcode, mas o ASLR, offsets, stack, memória, alinhamento dificultam acertar, então criamos o [NOP NOP NOP NOP shellcode]

O que acontece é o seguinte, mesmo errando pouco o CPU cai nos NOPs, desliza e chega no shellcode, chamamos de NOP sled. Sem o NOP sled, temos que acertar o endereço exato. samos muito em shellcode injection e entre outros.

### AUXILIARY
Coisa que usamos muito no metasploit são os auxiliares. Eles servem para gente estudar a vítima antes do ataque, podemos: scannear, fazer enumeração, brute force, fuzzing, coleta de informações, sniffing, spoofing e entre outros. Ele não necessariamente exploram vulnerabilidade.

- Exemplo
  - auxiliary/scanner/smb/smb_version, descobre versão do SMB
  - auxiliary/scanner/ssh/ssh_login, SSH brute force
  - auxiliary/scanner/portscan/tcp, Port scan
  - auxiliary/scanner/ftp/anonymous FTP anonymous login
  - fuzzers enviam dados aleatórios para tentar crashar o programa

### POST

Usamos ele quando estamos dentro da máquina, para expandir o controle. fazemos:
- enumeração interna
- coleta de credenciais
- pivoting
- privilege escalation
- persistência
- dump hashes
- coleta de arquivos
- screenshots

Exemplos reais
- run post/windows/gather/hashdump, extrai hashes de senhas
- post/windows/gather/enum_applications, enumera softwares
- post/windows/gather/enum_logged_on_users, enumera users
- post/windows/manage/screenshot, captura imagem
- post/multi/recon/local_exploit_suggester, escalação de privilégio

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

O encodedr pode ajudar ou atrapalhar, depende do seu exploit

# Laboratório de Ferramentas no Kali Linux

**Autor:** Lucas William  
**Máquina alvo (testes):** Metasploitable  
**IP da máquina alvo:** `192.168.56.103`

> ⚠️ **ATENÇÃO (legal & ético)** — Todo o conteúdo e exemplos aqui são para fins educacionais e devem ser executados **apenas** em ambientes controlados e com autorização. Não use essas técnicas em redes ou hosts sem permissão explícita.

---

## Resumo
Este repositório documenta um laboratório prático realizado em um curso, no qual foram utilizadas ferramentas do Kali Linux para reconhecimento e testes de intrusão em uma máquina vulnerável (Metasploitable). As etapas incluem varredura (`nmap`), enumeração e exploração de FTP, criação de *wordlists*, ataques de força bruta e automação de autenticação com `medusa`.

---

## Ferramentas e conceitos (breve explicação)

**Metasploitable**  
Máquina virtual propositalmente vulnerável usada como alvo de testes. Ideal para treinar técnicas de pentest em ambiente isolado.

**FTP (File Transfer Protocol)**  
Protocolo de transferência de arquivos. Em configurações inseguras, pode permitir login anônimo ou envio/recebimento de credenciais em texto claro.

**Nmap**  
Ferramenta de varredura para descobrir hosts, portas abertas e serviços em execução. Muito útil para reconhecimento inicial.

**Wordlists**  
Arquivos de palavras (usuários e senhas) usados em ataques de dicionário/força bruta. Podem ser geradas com ferramentas como `crunch` ou criadas manualmente.

**Brute-force**  
Tentativas repetidas de combinações de credenciais até encontrar pares válidos. Detectável e intrusivo — usar apenas em laboratório.

**Medusa**  
Ferramenta multi-threaded para força bruta/ataques de dicionário em vários serviços (FTP, SSH, HTTP, etc.). Permite automatizar testes de autenticação.

---

## Ambiente usado
- Host: máquina local rodando VirtualBox/VMware.  
- VM atacante: Kali Linux (atualizado).  
- VM alvo: Metasploitable (IP: `192.168.56.103`).  
- Rede: host-only / rede interna (isolada).

---

## Arquivos do repositório (sugestão)
- `README.md` — este arquivo.  
- `wordlists/users.txt` — lista de usuários utilizadas nos testes.  
- `wordlists/passwords.txt` — lista de senhas utilizadas nos testes.  
- `outputs/nmap-scan.txt` — saída do `nmap`.  
- `outputs/medusa-result.txt` — resultado do `medusa`.  
- `scripts/` — scripts úteis (opcional).

---

## Passos realizados (comandos e notas — use apenas em ambiente autorizado)

### 1) Varredura com Nmap
Comando usado para identificar portas e serviços:
```bash
# varredura com detecção de versão e scripts básicos
nmap -sV -sC -Pn 192.168.56.103 -oN outputs/nmap-scan.txt
```
Observação: o `-sC` executa scripts padrão; `-oN` salva a saída em um arquivo.

### 2) Enumeração FTP
Checagem de login anônimo via Nmap:
```bash
nmap --script ftp-anon -p 21 192.168.56.103 -oN outputs/ftp-anon.txt
```
Ou teste manual:
```bash
ftp 192.168.56.103
# tentar login: username: anonymous (ou leave blank) / password: any
```

### 3) Criação de wordlists
Exemplos de geração/edição de listas:

Gerar passwords com **crunch** (exemplo):
```bash
# gera senhas de 6 a 8 caracteres alfanuméricos e salva em wordlists/passwords.txt
crunch 6 8 abcdefghijklmnopqrstuvwxyz0123456789 -o wordlists/passwords.txt
```

Criar um arquivo de usuários (exemplo manual):
```bash
cat > wordlists/users.txt <<EOF
root
admin
user
ftp
test
EOF
```

### 4) Ataque de força bruta com Medusa
Exemplo de execução do `medusa` contra o serviço FTP no IP alvo usando arquivos criados:
```bash
medusa -h 192.168.56.103 -U wordlists/users.txt -P wordlists/passwords.txt -M ftp -f -t 10 -O outputs/medusa-result.txt
```
Explicação de parâmetros úteis:
- `-h` host alvo.  
- `-U` arquivo com usuários.  
- `-P` arquivo com senhas.  
- `-M` módulo/serviço (`ftp`, `ssh`, etc.).  
- `-f` interrompe quando uma credencial válida é encontrada.  
- `-t` número de threads (ajuste conforme ambiente).  
- `-O` salva saída detalhada.

> Observação: `medusa` gera muitas requisições — use `-t` com cuidado e rode apenas na VM isolada.

---

## Como documentei os resultados
1. Salvei a saída do `nmap` em `outputs/nmap-scan.txt`.  
2. Salvei logs de enumeração FTP em `outputs/ftp-anon.txt`.  
3. Salvei resultado do `medusa` em `outputs/medusa-result.txt`.  
4. Manteve-se um `lab-notes.txt` com horários, parâmetros, e observações (ex.: taxa de tentativas, threads usadas, se houve sucesso).

---

## Exemplo de formato de saída (cole aqui suas saídas reais)
Você pode colar os blocos abaixo com as saídas reais para seu README ficar mais completo.

**Trecho do nmap (cole a sua saída real entre as linhas):**
```
--- INICIO: saida-nmap ---
# Cole aqui a saída real do arquivo outputs/nmap-scan.txt
--- FIM: saida-nmap ---
```

**Trecho do medusa (cole a sua saída real entre as linhas):**
```
--- INICIO: saida-medusa ---
# Cole aqui a saída real do arquivo outputs/medusa-result.txt
--- FIM: saida-medusa ---
```

---

## O que aprendi (resumo)
- Como identificar serviços e portas com `nmap`.  
- Como enumarar serviços FTP e verificar login anônimo.  
- Como gerar wordlists e pensar em estratégias para dicionário/brute-force.  
- Como usar `medusa` para automatizar testes de credenciais e controlar threads.  
- Importância de executar testes em ambientes autorizados e isolar laboratórios.

---

## Boas práticas e mitigação (para defender sistemas)
- Desabilitar autenticação em texto claro (usar SFTP/FTPS/SSH com chaves).  
- Implementar bloqueio/limite de tentativas (rate limiting, account lockout).  
- Monitorar logs e alertar tentativas de autenticação suspeitas.  
- Usar senhas fortes e políticas de senha.  
- Segmentar rede e isolar serviços vulneráveis.

---

## Observações finais
Este README está pronto para uso no GitHub e já inclui o IP real do seu laboratório (`192.168.56.103`). Se quiser, posso:
- inserir **suas saídas reais** de `nmap` e `medusa` diretamente no README (cole-as aqui e eu atualizo o arquivo), ou  
- gerar agora o `README.md` final com o texto atual para download.


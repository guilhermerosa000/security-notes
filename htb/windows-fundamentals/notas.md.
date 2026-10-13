# HTB Academy: Windows Fundamentals

Notas de estudo do módulo. O módulo é teórico e de administração (sem exploração), então o foco aqui foi entender como o Windows funciona para saber depois onde ele costuma falhar.

---

## 1. Estrutura do sistema

### Diretórios principais

| Caminho | Para que serve |
|---|---|
| `C:\Windows` | Arquivos do sistema operacional |
| `C:\Windows\System32` | Binários e bibliotecas centrais (cmd, powershell, drivers, hives do registro em `config\`) |
| `C:\Program Files` / `Program Files (x86)` | Aplicações 64 bits / 32 bits |
| `C:\Users\<usuário>` | Perfil de cada usuário (Desktop, Documents, `AppData`, `NTUSER.DAT`) |
| `C:\ProgramData` | Dados de aplicações compartilhados entre usuários (oculta por padrão) |

### Processos importantes

| Processo | Função |
|---|---|
| `System` | Processo do kernel |
| `smss.exe` | Session Manager, cria as sessões |
| `csrss.exe` | Client/Server Runtime, gerencia janelas e consoles |
| `wininit.exe` | Inicializa o sistema e sobe `services.exe` e `lsass.exe` |
| `services.exe` | Service Control Manager, gerencia os serviços |
| `lsass.exe` | Autenticação e políticas de segurança; mantém credenciais em memória |
| `winlogon.exe` | Gerencia o logon/logoff interativo |
| `svchost.exe` | Hospeda vários serviços em um só processo |
| `explorer.exe` | Interface gráfica (shell) |

Ponto de atenção: o `lsass.exe` guarda credenciais em memória, por isso é alvo comum de ataques e deve ser protegido e monitorado.

### Versões

Família NT. Cliente: Windows 10 e 11. Servidor: Windows Server 2016, 2019, 2022, 2025. Servidores costumam rodar serviços como AD, DNS, DHCP e compartilhamento de arquivos.

---

## 2. Sistemas de arquivos

| | FAT32 | NTFS |
|---|---|---|
| Tamanho máximo de arquivo | 4 GB | Muito grande (praticamente sem limite para uso comum) |
| Permissões (ACL) | Não | Sim |
| Criptografia nativa (EFS) | Não | Sim |
| Journaling | Não | Sim |
| Compressão e quotas | Não | Sim |
| Uso típico | Pendrives, compatibilidade | Padrão do Windows |

Extras do NTFS: **Alternate Data Streams (ADS)**, que permitem anexar dados ocultos a um arquivo e já foram usados para esconder conteúdo (visível com `dir /r`).

---

## 3. Permissões NTFS e compartilhamento

### Permissões NTFS

`Full Control`, `Modify`, `Read & Execute`, `List Folder Contents`, `Read`, `Write`.

- São herdadas da pasta pai por padrão (pode ser quebrada).
- Um **Deny** explícito prevalece sobre um **Allow**.
- Valem localmente e pela rede.

### Permissões de compartilhamento (share)

`Full Control`, `Change`, `Read`. Valem **só** para acesso pela rede.

### Permissão efetiva em um compartilhamento

Quando alguém acessa pela rede, vale a **mais restritiva** entre a do share e a do NTFS.

### Comandos úteis

```cmd
icacls C:\pasta                         :: lista as permissões
icacls C:\pasta /grant Usuario:(OI)(CI)RX   :: concede leitura/execução com herança
```

Flags do `icacls`: `F` (full), `M` (modify), `RX` (read/execute), `R` (read), `W` (write). `(OI)` e `(CI)` controlam a herança para arquivos e subpastas.

```powershell
Get-Acl C:\pasta | Format-List
```

---

## 4. ACL, ACE, SID

- **SID**: identificador único de usuário, grupo ou computador (o sistema usa o SID, não o nome).
- **ACL**: lista de controle de acesso de um objeto.
  - **DACL**: define quem pode acessar e com qual permissão.
  - **SACL**: define o que será auditado (registrado em log).
- **ACE**: cada entrada da ACL (um usuário/grupo com uma permissão de Allow ou Deny).

---

## 5. Contas, grupos e sessões

### Contas e grupos

- Contas internas comuns: `Administrator`, `Guest`, `NT AUTHORITY\SYSTEM` (mais alto privilégio local), `LocalService` e `NetworkService` (contas de serviço com privilégios reduzidos).
- Grupos locais relevantes: `Administrators`, `Users`, `Remote Desktop Users` (pode entrar via RDP), `Backup Operators`.
- **UAC** (User Account Control) pede confirmação para ações que exigem privilégio elevado, mesmo para membros do grupo Administrators.

```cmd
whoami /all                :: usuário, SID, grupos e privilégios
net user                   :: lista usuários locais
net localgroup administrators
```

### Sessões

- Cada usuário logado tem um **ID de sessão**. Serviços rodam na **sessão 0** (isolada); usuários interativos, na sessão 1 em diante.

```cmd
query user      :: sessões ativas
qwinsta
```

---

## 6. Serviços

- Gerenciados pelo **Service Control Manager** (`services.exe`).
- Cada serviço tem: conta de execução, tipo de inicialização (Automático, Manual, Desativado) e permissões próprias.

```cmd
sc query                   :: lista serviços
sc qc <serviço>            :: configuração do serviço (caminho do binário, conta)
```

```powershell
Get-Service
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Ponto de atenção: o que importa para segurança é com qual conta o serviço roda, quem pode alterar sua configuração e quem pode escrever no caminho do binário.

---

## 7. Registro (Registry)

Banco de dados hierárquico com configurações do sistema e dos usuários.

| Hive | Conteúdo |
|---|---|
| `HKLM` (HKEY_LOCAL_MACHINE) | Configuração da máquina |
| `HKCU` (HKEY_CURRENT_USER) | Configuração do usuário logado |
| `HKCR` (HKEY_CLASSES_ROOT) | Associações de arquivos |
| `HKU` (HKEY_USERS) | Perfis de todos os usuários carregados |
| `HKCC` (HKEY_CURRENT_CONFIG) | Perfil de hardware atual |

- Arquivos das hives do sistema ficam em `C:\Windows\System32\config\` (`SAM`, `SYSTEM`, `SECURITY`, `SOFTWARE`). A do usuário é o `NTUSER.DAT`.
- Chaves `Run` (`...\CurrentVersion\Run`, em HKCU e HKLM) definem programas que iniciam com o sistema ou com o usuário, e por isso são um lugar clássico de persistência.

```cmd
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run
```

Ferramenta gráfica: `regedit`.

---

## 8. Rede e compartilhamento (SMB)

- **SMB** (porta **445**; 139 em NetBIOS) é o protocolo de compartilhamento de arquivos e impressoras do Windows.
- Caminho UNC: `\\servidor\compartilhamento`.
- Compartilhamentos administrativos padrão: `C$` (raiz do disco), `ADMIN$` (`C:\Windows`) e `IPC$` (comunicação entre processos). Exigem credenciais de administrador.

```cmd
net share                          :: compartilhamentos locais
net use Z: \\servidor\share        :: mapeia unidade de rede
net view \\servidor
```

Do Linux:

```bash
smbclient -L //IP -U usuario       # lista os compartilhamentos
smbclient //IP/share -U usuario    # acessa um compartilhamento
```

### Portas comuns

| Porta | Serviço |
|---|---|
| 135 | RPC |
| 139 / 445 | NetBIOS / SMB |
| 3389 | RDP |
| 5985 / 5986 | WinRM (HTTP / HTTPS) |

```cmd
ipconfig /all
netstat -ano       :: conexões e portas em escuta, com o PID do processo
```

---

## 9. Firewall do Windows

- **Windows Defender Firewall** com 3 perfis: **Domain**, **Private**, **Public**. Cada um tem suas regras.
- Regras de **entrada** e **saída**, por programa, porta ou protocolo.

```cmd
netsh advfirewall show allprofiles
```

```powershell
Get-NetFirewallRule | Where-Object {$_.Enabled -eq "True"}
```

---

## 10. Acesso remoto (RDP) a partir do Linux

```bash
xfreerdp /v:<IP> /u:<usuário> /p:<senha>
```

Opções úteis:

| Opção | Efeito |
|---|---|
| `/cert:ignore` | Ignora o aviso de certificado (aceitável em laboratório, não em produção) |
| `/dynamic-resolution` | Ajusta a resolução ao redimensionar a janela |
| `/drive:share,/caminho` | Compartilha uma pasta local com a sessão remota |

Observações:
- O usuário precisa ter permissão para RDP (ser Administrator ou do grupo `Remote Desktop Users`).
- Em versões novas do FreeRDP o binário pode se chamar `xfreerdp3`.
- Evite deixar a senha em texto puro no histórico do shell em ambientes reais.

---

## 11. PowerShell

| Comando | Uso |
|---|---|
| `Get-Command`, `Get-Help <cmd>` | Descobrir e entender comandos |
| `Get-Process`, `Get-Service` | Processos e serviços |
| `Get-ChildItem` (alias `ls`, `dir`) | Listar arquivos |
| `Get-Acl` | Ver permissões |
| `Get-ExecutionPolicy -List` | Ver a política de execução de scripts |

**Execution Policy** (por exemplo, `RemoteSigned`) é uma proteção contra execução acidental de scripts, não uma barreira de segurança real, porque pode ser contornada.

---

## 12. Resumo: o que observar em segurança

- Quem tem acesso a quê (ACLs, grupos, shares).
- Com qual conta cada serviço e processo roda.
- Compartilhamentos expostos e portas abertas (445, 3389, 5985).
- Permissões de share vs NTFS (vale a mais restritiva).
- Chaves de registro de inicialização e processos com acesso a credenciais (`lsass.exe`).
- Firewall ativo e com regras mínimas por perfil.

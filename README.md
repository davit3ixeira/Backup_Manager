# Backup Manager

Aplicação desktop em Python para executar backups manuais de arquivos por meio de **perfis**. Você define de onde os arquivos saem, quais tipos entram no backup, quais filtros se aplicam e para onde cada tipo vai. A interface gráfica usa `tkinter` e `customtkinter`.

Projeto desenvolvido para a disciplina INF1040.

## Como funciona

A configuração é hierárquica:

```
Perfil
└── Origem (pasta de entrada)
    └── Tipo de arquivo
        ├── Restrições (extensão, nome, tamanho, data)
        └── Destinos (pasta de saída + operação)
```

Cada destino define uma operação:

| Operação | O que faz |
| --- | --- |
| `copiar` | Copia o arquivo para o destino e mantém o original |
| `mover` | Move o arquivo para o destino |
| `recortar` | Move o arquivo para o destino (recorte) |

Perfis, origens e tipos podem ser ativados ou desativados. Um perfil inativo não executa backup.

## Funcionalidades

- Criação, edição e exclusão de perfis de backup
- Várias origens por perfil e vários tipos de arquivo por origem
- Filtros por extensão, nome, tamanho e data de modificação
- Extensões customizadas, salvas nas configurações globais
- Vários destinos por tipo, cada um com sua operação
- Validação do perfil antes de executar (origem, destino, permissões e espaço disponível)
- Resumo final do backup com contadores, registros e erros
- Persistência local em JSON, gravada ao fechar o aplicativo
- Códigos de retorno padronizados com mensagens legíveis

## Requisitos

- Python 3.10 ou superior
- Dependências em `requirements-dev.txt`: `customtkinter` e `pytest`
- `tkinter` instalado (já vem com o Python no Windows e macOS; no Linux pode ser preciso instalar o pacote `python3-tk`)

## Como rodar

1. Entre na pasta do projeto:

```bash
cd BackupManager/INF1040-BACKUPMANAGER-dev
```

2. Crie e ative um ambiente virtual:

```bash
python -m venv venv
```

Linux ou macOS:

```bash
source venv/bin/activate
```

Windows (PowerShell):

```powershell
venv\Scripts\Activate.ps1
```

3. Instale as dependências:

```bash
pip install -r requirements-dev.txt
```

4. Abra o aplicativo:

```bash
python -m backupmanager.main
```

## Testes

```bash
python -m pytest -q
```

Para só validar a sintaxe:

```bash
python -m compileall backupmanager tests
```

## Estrutura do projeto

```
INF1040-BACKUPMANAGER-dev/
├── backupmanager/
│   ├── main.py              ponto de entrada
│   ├── controller.py        estado da aplicação e fachada para a UI
│   ├── return_codes.py      códigos de retorno e mensagens
│   ├── domain/
│   │   ├── perfil_manager.py     perfis, origens, tipos, destinos e restrições
│   │   ├── backup_validation.py  validação do perfil antes do backup
│   │   └── backup_result.py      resultado e erros da execução
│   ├── engine/
│   │   ├── backup_engine.py      execução do backup
│   │   └── file_utils.py         listagem de arquivos e aplicação dos filtros
│   ├── infra/
│   │   └── storage.py            leitura e escrita dos JSONs
│   └── ui/                       interface gráfica
├── tests/                   testes com pytest
├── docs/
│   └── DOCUMENTACAO_BACKUPMANAGER.md
├── pyproject.toml
└── requirements-dev.txt
```

## Dados persistidos

Os dados ficam na pasta `data/`, criada ao salvar:

- `data/perfis.json`: perfis, origens, tipos, restrições, destinos e estado ativo ou inativo
- `data/config.json`: configurações globais, como as extensões customizadas

## Arquitetura

O projeto não usa classes. Os tipos abstratos de dados (perfil, origem, destino, resultado e outros) são dicionários, e cada módulo dono de um TAD é o único que conhece suas chaves internas. Os outros módulos acessam esses dados por funções públicas. O `controller` funciona como fachada para a interface e delega a lógica ao domínio, ao motor de backup e ao armazenamento.

Fluxo resumido de um backup: a interface chama o controller, que valida o perfil, o motor seleciona os arquivos conforme as restrições, executa a operação de cada destino e devolve um resultado que a interface exibe.

A documentação completa, com a descrição de cada módulo e dos testes, está em [`docs/DOCUMENTACAO_BACKUPMANAGER.md`](BackupManager/INF1040-BACKUPMANAGER-dev/docs/DOCUMENTACAO_BACKUPMANAGER.md).

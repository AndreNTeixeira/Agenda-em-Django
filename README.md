# Agenda

Aplicação web de agenda de eventos feita com **Django**. Cada usuário faz login e gerencia seus próprios compromissos: criar, editar, excluir e listar eventos futuros, com destaque visual para os que já passaram da data.

Projeto de estudos desenvolvido durante o 5º período.

---

## Funcionalidades

- **Autenticação de usuários** — login e logout usando o sistema de autenticação nativo do Django, com mensagem de erro para credenciais inválidas.
- **Listagem de eventos** — mostra apenas os eventos do usuário logado que ainda não expiraram (tolerância de 1 hora após o horário do evento).
- **Cadastro e edição** — o mesmo formulário atende criação e edição; a edição só é permitida se o evento pertencer ao usuário logado.
- **Exclusão protegida** — tentar excluir um evento de outro usuário (ou inexistente) retorna `Http404`.
- **Eventos atrasados em destaque** — eventos com data anterior ao momento atual aparecem em vermelho na listagem.
- **API JSON simples** — endpoint que retorna a lista de eventos do usuário em JSON.
- **Django Admin** — model `Evento` registrado com listagem e filtros por usuário e data.

---

## Stack

| Camada | Tecnologia |
|---|---|
| Backend | Python 3.13 + Django 6.0 |
| Banco de dados | SQLite (`db.sqlite3`) |
| Frontend | Django Templates (HTML puro) |
| Timezone | `America/Sao_Paulo`, idioma `pt-br` |

---

## Estrutura do projeto

```
agenda/
├── agenda/                 # Configuração do projeto Django
│   ├── settings.py         # Settings (SQLite, pt-br, America/Sao_Paulo)
│   ├── urls.py             # Roteamento das URLs
│   ├── asgi.py
│   └── wsgi.py
├── core/                   # App principal
│   ├── models.py           # Model Evento
│   ├── views.py            # Views de login, listagem e CRUD
│   ├── admin.py            # Configuração do Django Admin
│   └── migrations/
├── templates/              # Templates HTML
│   ├── model-page.html     # Layout base
│   ├── model-header.html
│   ├── model-footer.html
│   ├── login.html
│   ├── agenda.html         # Listagem de eventos
│   └── evento.html         # Formulário de criação/edição
├── db.sqlite3
├── requirements.txt
└── manage.py
```

---

## Modelo de dados

`Evento` (tabela `evento`):

| Campo | Tipo | Descrição |
|---|---|---|
| `titulo` | `CharField(100)` | Título do evento |
| `descricao` | `TextField` | Descrição (opcional) |
| `data_evento` | `DateTimeField` | Data e hora do compromisso |
| `data_criacao` | `DateTimeField` | Preenchido automaticamente na criação |
| `usuario` | `ForeignKey(User)` | Dono do evento (cascade on delete) |
| `local` | `CharField(100)` | Local do evento (opcional) |

Métodos auxiliares: `get_data_evento()` (formata para `dd/mm/aaaa HH:MM`), `get_data_input_evento()` e `get_evento_atrasado()` (indica se o evento já passou).

---

## Como rodar

### 1. Clonar o repositório

```bash
git clone https://github.com/AndreNTeixeira/Agenda-em-Django.git
```

### 2. Criar e ativar o ambiente virtual

```bash
python3 -m venv venv && source venv/bin/activate
```

No Windows: `venv\Scripts\activate`

### 3. Instalar as dependências

```bash
pip install -r requirements.txt
```

### 4. Aplicar as migrações

```bash
python manage.py migrate
```

### 5. Criar um usuário

O projeto não tem tela de cadastro — os usuários são criados via terminal ou pelo Django Admin.

```bash
python manage.py createsuperuser
```

### 6. Subir o servidor

```bash
python manage.py runserver
```

Acesse [http://127.0.0.1:8000](http://127.0.0.1:8000) — você será redirecionado para a tela de login.

---

## Rotas

| Método | Rota | Descrição |
|---|---|---|
| GET | `/` | Redireciona para `/agenda/` |
| GET | `/login/` | Tela de login |
| POST | `/login/submit` | Autentica o usuário |
| GET | `/logout/` | Encerra a sessão |
| GET | `/agenda/` | Lista os eventos futuros do usuário 🔒 |
| GET | `/agenda/evento/` | Formulário de novo evento (ou edição via `?id=`) 🔒 |
| POST | `/agenda/evento/submit` | Salva criação ou edição 🔒 |
| GET | `/agenda/evento/delete/<int:id>/` | Exclui um evento do usuário 🔒 |
| GET | `/agenda/lista/` | Retorna `id` e `titulo` dos eventos em JSON 🔒 |
| GET | `/eventos/<titulo>/` | Retorna o local de um evento pelo título |
| GET | `/admin/` | Django Admin |

🔒 = exige autenticação (redireciona para `/login/`).

---

## Observações

Este é um projeto de estudos e as configurações são de **desenvolvimento**. Antes de qualquer uso em produção seria necessário:

- Mover a `SECRET_KEY` para variável de ambiente (hoje está fixa em `settings.py`).
- Definir `DEBUG = False` e preencher `ALLOWED_HOSTS`.
- Trocar o SQLite por um banco de produção (PostgreSQL, por exemplo).
- Habilitar `USE_TZ = True` para trabalhar com datas timezone-aware.

---

## Licença

Projeto de estudos, livre para uso e adaptação.

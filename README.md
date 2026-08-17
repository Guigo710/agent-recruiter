## Agent Recruiter — Guia de Uso 

Agent Recruiter é um agente de IA para procurar vagas de acordo com seu perfil e entrega cursos complementares pela plataforma Alura. 
Projeto de criação de agentes de IA usando Zed (editor com agente integrado) e OpenRouter (provedor de modelos), com apoio da ferramenta Firecrawl para navegação/scraping web.

agent-recruiter é o nome do projeto. Dentro dele, o primeiro agente criado tem a persona Maestro.

O projeto é dividido em 3 agentes, construídos em etapas (planos):

## Agente	Persona	Função	Plano
- Agente 1:	Maestro	Agente base do projeto, orquestra o fluxo geral (quiz de personalidade, perfil do usuário)	plano.md
- Agente 2:	Scout	Busca vagas de emprego em sites como InfoJobs, Vagas.com, Indeed e Nerdin	plano-fase2.md
- Agente 3:	Curator	Busca cursos na Alura que complementem as habilidades que faltam ao usuário, com base no quiz e nas vagas encontradas pelo Scout	plano-fase3.md
## 1. Pré-requisitos
Conta no OpenRouter
Editor Zed instalado
Node.js/npm instalado (para rodar o Firecrawl via npx)
Uma chave de API do Firecrawl
2. Configuração do ambiente
2.1 Instalar o Zed

Baixe e instale o Zed a partir do site oficial: https://zed.dev

## 2.2 Criar conta e API key no OpenRouter
Acesse https://openrouter.ai e crie uma conta.
Gere uma API Key no painel do OpenRouter.
## 2.3 Configurar o provider no Zed
Abra o Zed.
Pressione Ctrl+B para abrir a configuração de providers (Configure Providers).
Encontre OpenRouter na lista.
Cole a API Key gerada no passo anterior.
2.4 Instalar e configurar o Firecrawl

O Firecrawl é usado pelos agentes para navegar e extrair conteúdo de páginas web. Ele é instalado e inicializado via npx:

bash
npx -y firecrawl-cli@latest init --all -k <SUA_CHAVE_FIRECRAWL>

Substitua <SUA_CHAVE_FIRECRAWL> pela sua chave real de API do Firecrawl.

Se, por algum motivo, o Firecrawl não funcionar, os agentes devem usar a ferramenta de acesso web nativa como alternativa. Esse procedimento de fallback deve ficar documentado dentro das skills do agente correspondente.

## 3. Fluxo de trabalho com planos e threads no Zed

O desenvolvimento de cada agente segue sempre o mesmo padrão:

Criar o plano primeiro, escrevendo um bom prompt e salvando o resultado em um arquivo plano*.md (ex: plano.md, plano-fase2.md, plano-fase3.md).
No Zed, abrir uma nova thread.
Referenciar o arquivo de plano no prompt usando @, por exemplo @plano.md.
Marcar a opção "Keep this file" para manter o arquivo de contexto na thread.
Abrir uma nova thread e pedir ao agente para ler o @AGENTS.md (dentro de skills/, arquivo com as instruções/definições dos agentes).
A partir daí, o agente executa as tarefas planejadas (ex: instalar o Firecrawl, criar as skills, buscar vagas, buscar cursos etc).

Cada novo agente é construído em cima do plano anterior, referenciando os planos já existentes. Por exemplo, o plano do Scout referencia o plano.md, e o plano do Curator referencia tanto o plano.md quanto o plano-fase2.md.

## 4. Detalhamento dos agentes
## 4.1 Maestro (plano.md)

Agente inicial do projeto, criado a partir do primeiro plano definido em plano.md. É responsável por orquestrar o fluxo com o usuário, incluindo o quiz de personalidade e a montagem do perfil do usuário (data/personality-quiz.md, data/user-profile.md).

## 4.2 Scout (plano-fase2.md)
Criado a partir de plano.md.
Responsável por buscar vagas de emprego.
Usa o Firecrawl (já instalado) como ferramenta principal de navegação, executado como comando.
Caso o Firecrawl não funcione, usa a ferramenta de acesso web nativa como alternativa.
Esse procedimento (Firecrawl → fallback web) deve ser documentado dentro das skills do agente.
Sites de busca de vagas utilizados como referência/exemplo:
InfoJobs
Vagas.com
Indeed
Nerdin

Os resultados da busca são salvos em data/job-search-results.md.

Após criado, o Scout deve ser executado para validar a busca de vagas.

## 4.3 Curator (plano-fase3.md)
- Criado a partir de plano.md e plano-fase2.md.
- Responsável por buscar cursos na Alura (alura.com.br) que complementem as habilidades que faltam ao usuário.
- Navega no site da Alura usando o Firecrawl, da mesma forma que o Scout faz nos sites de vagas.
- Cruza as informações do quiz do usuário (data/personality-quiz.md) com as vagas encontradas pelo Scout (data/job-search-results.md) para identificar as habilidades faltantes e, a partir disso, recomendar cursos adequados.
- As recomendações são salvas em data/course-recommendations.md.
## 5. Como rodar o projeto (resumo passo a passo)
- Instale o Zed e crie sua conta/API key no OpenRouter.
- Configure o provider OpenRouter no Zed (Ctrl+B).
- Instale o Firecrawl:
bash
   npx -y firecrawl-cli@latest init --all -k <SUA_CHAVE_FIRECRAWL>
   
- Escreva o prompt do primeiro agente e salve como plano.md.
- No Zed, abra uma nova thread, referencie @plano.md e mantenha o arquivo com "Keep this file".
- Abra uma nova thread e peça ao agente para ler @AGENTS.md.
- Peça para criar o plano-fase2.md (Scout), referenciando @plano.md, com as instruções de busca de vagas via Firecrawl (com fallback para busca web nativa) e as skills correspondentes.
- Rode o Scout para validar a busca de vagas.
- Peça para criar o plano-fase3.md (Curator), referenciando @plano.md e @plano-fase2.md, com as instruções de busca de cursos na Alura via Firecrawl, cruzando quiz + vagas encontradas.
- Rode o Curator para validar a recomendação de cursos.

## 6. Estrutura de arquivos do projeto

```text
agent-recruiter/
├── data/
│   ├── course-recommendations.md
│   ├── job-search-results.md
│   ├── personality-quiz.md
│   └── user-profile.md
│
├── personas/
│   ├── maestro.md
│   ├── scout.md
│   └── curator.md
│
├── skills/
│   └── AGENTS.md
│
├── plano.md
├── plano-fase2.md
└── plano-fase3.md
```

### 📂 Diretórios e arquivos

| Caminho                          | Descrição                                                           |
| -------------------------------- | ------------------------------------------------------------------- |
| `data/`                          | Arquivos de entrada e saída gerados durante a execução dos agentes. |
| `data/course-recommendations.md` | Cursos recomendados pelo **Curator**.                               |
| `data/job-search-results.md`     | Vagas encontradas pelo **Scout**.                                   |
| `data/personality-quiz.md`       | Respostas do quiz de personalidade.                                 |
| `data/user-profile.md`           | Perfil consolidado do usuário.                                      |
| `personas/`                      | Definição das personas e comportamentos dos agentes.                |
| `personas/maestro.md`            | Persona e comportamento do **Maestro**.                             |
| `personas/scout.md`              | Persona e comportamento do **Scout**.                               |
| `personas/curator.md`            | Persona e comportamento do **Curator**.                             |
| `skills/`                        | Instruções e procedimentos reutilizáveis pelos agentes.             |
| `skills/AGENTS.md`               | Instruções gerais compartilhadas entre os agentes.                  |
| `plano.md`                       | Plano e fluxo do **Maestro (Agente 1)**.                            |
| `plano-fase2.md`                 | Plano e fluxo do **Scout (Agente 2)**.                              |
| `plano-fase3.md`                 | Plano e fluxo do **Curator (Agente 3)**.                            |


- data/ — arquivos de entrada/saída gerados durante a execução dos agentes (quiz, perfil, vagas e recomendações).
- personas/ — definição de cada agente (system prompt / comportamento). 
- skills/ — instruções e procedimentos reutilizáveis pelos agentes, incluindo o AGENTS.md e o procedimento de navegação via Firecrawl (com fallback para busca web nativa).

# Manual do Usuário: moonhttp (`moonhttp-v1.0.6`)

O **moonhttp** (também chamado de `moonhttp`) é um servidor HTTP local superleve, rápido e visual, desenvolvido em Lua e projetado para rodar como um executável binário autônomo e multiplataforma (Linux, Windows e macOS). 

Seu objetivo principal é acelerar o desenvolvimento local de páginas web estáticas, oferecendo uma experiência de linha de comando (CLI) rica e feedback imediato sobre a integridade dos arquivos do projeto.

---

## 1. Módulos e Tecnologias Utilizadas

Para a construção e empacotamento do binário autônomo, foram utilizadas apenas as bibliotecas essenciais da ecologia Lua:

* **LuaJIT / Lua 5.1**
* **LuaSocket**
* **LuaFileSystem (`lfs`)**
* **Códigos de Escape ANSI** (Cores e Formatação de Texto)
* **LuaStatic / Luamake** (Compilação e Bundling Binário)

---

## 2. Aspectos de um Servidor HTTP Local Suportados

O **moonhttp** implementa as características essenciais para um ambiente de desenvolvimento *localhost*:

* **Binding de Rede Flexível:** Suporte a escuta em interfaces de *loopback* (`127.0.0.1`, `localhost`), resolução do IP da rede local e hosts customizados.
* **Resolução Automática de Diretórios:** Redirecionamento `301 Moved Permanently` para rotas de pastas sem barra final (ex: `/sobre` -> `/sobre/`).
* **Tratamento de Páginas Index:** Busca automática por documentos `index.html`, `index.htm` ou `index.lwa`.
* **Identificação de MIME Types:** Reconhecimento automático dos formatos `.html`, `.htm`, `.lwa`, `.css`, `.js`, `.ico`, `.png` e fallback para `text/plain`.
* **Controle de Cache em Desenvolvimento:** Envio automático de cabeçalhos `Cache-Control: no-cache` para evitar que o navegador armazene versões velhas dos arquivos durante a edição.
* **Segurança de Métodos:** Restrição estrita aos métodos `GET` and `HEAD`, bloqueando tentativas de modificação de arquivos por `POST`, `PUT` ou `DELETE` (retornando `405 Method Not Allowed`).
* **Liberação Imediata de Porta:** Configuração `SO_REUSEADDR` para evitar bloqueios da porta no sistema operacional ao encerrar a aplicação com `CTRL+C`.
* **Listagem Opcional de Arquivos:** Navegação por diretórios (Directory Listing) desativada por padrão (retornando `404 Not Found` por segurança), mas que pode ser ativada sob demanda.

---

## 3. Inspetor de Arquivos e Suporte a Web Apps

### Inspeção Automática de Assets (Linha de Frente)
Ao servir qualquer documento HTML ou `.lwa`, o **moonhttp** analisa o código em tempo real e inspeciona as tags `<link>`, `<script>`, `<img>` e `<a href>`. Se o arquivo referenciado não existir na pasta local, o servidor emite um **alerta visual no terminal** informando a ausência do recurso.

### Suporte a Frameworks JS e Aplicações Web

* **Projetos Suportados:** Sites estáticos em HTML5/CSS3/JS, landing pages, templates locais, aplicações construídas com **Vanilla JS** e arquivos `.lwa` (tratados como HTML padrão).
* **Builds de Frameworks JS (React, Vue, Svelte, Angular):** O **moonhttp** consegue servir com perfeição a pasta de exportação estática dessas ferramentas (pastas como `dist/` ou `build/`) após a execução do comando de *build* do framework.
* **Limitações (O que NÃO suporta):**
  * Não substitui o *DevServer* com *Hot Module Replacement* (HMR) utilizado durante o desenvolvimento ativo de frameworks JS (Vite, Webpack, Next.js).
  * Não executa código server-side dinâmico (Node.js, PHP, Python, etc.) nem bancos de dados.

---

## 4. Histórico de Versionamento

* **v1.0.0:** Lançamento inicial com suporte a CWD, binding de porta/host via CLI, resolução de `index.html`/`index.htm`/`index.lwa`, log de erros 404 e encerramento limpo.
* **v1.0.1:** Adição do **Inspetor de Dependências Estáticas**, exibição de logotipo em ASCII Art e links clicáveis no terminal com códigos de cores ANSI.
* **v1.0.2:** Adição do tratamento de rotas de pastas e diretórios sem barra final com redirecionamento HTTP `301`.
* **v1.0.3:** Adição de restrição de segurança para métodos HTTP (`GET`/`HEAD`), cabeçalhos contra cache de desenvolvimento e suporte à flag `--dir-list`.
* **v1.0.4:** sem texto por enquanto.
* **v1.0.5:** sem texto por enquanto.
* **v1.0.5:** sem texto por enquanto.
---

## 5. Guia Completo de Comandos CLI

A sintaxe base para execução do binário é:

```bash
moonhttp [flags]
```

### Parâmetros e Argumentos/Flag

| Descrição | Valor Padrão |
| :--- | :--- |
|**-h:<host>**  | Define a interface de rede / host para escutar 127.0.0.1|
|**-p:<porta>** | Configura a porta de rede do servidor7777 (ou 8888 para perfis moon/moonip)|
|**--dir-list** | * Ativa a exibição da lista de arquivos da pasta caso não exista um arquivo index.|
|**-help ou --help**| mostra o helper da aplicação com os comandos disponíveis.|
|**-manual ou --manual**| mostra o link do manual.md no github.|
|**-v, -version, ou --version**| mostra versão do moonhttp.|

```
Desativado (Retorna 404)
Exemplos Práticos de Uso

1. Execução Padrão (Mais Básico)Inicia o servidor no diretório atual (CWD),
usando o IP 127.0.0.1 e a porta 7777
```
```ps1
# windows no terminal (cmd/ps1)
moonhttp
```

#### 2. Usando Atalho de Host localhost

> Configura o host como 127.0.0.1 na porta 7777

```bash
./moonhttp -h:localhost
```

#### 3. Alterando Apenas a Porta
> Inicia o servidor na porta customizada 5677 mantendo o IP 127.0.0.1.

```bash
./moonhttp -p:5677
```

#### 4. Usando o Perfil moon
> Utiliza o alias pré-configurado moon, que abre o servidor no IP 127.0.0.1 usando a porta 8888.

```bash
./moonhttp -h:moon
```

#### 5. Compartilhando na Rede Local com moonip
>Identifica automaticamente o IP da placa de rede local da máquina (ex: 192.168.1.15) na porta 8888, permitindo que outros dispositivos na mesma rede Wi-Fi/Ethernet acessem o site.

```bash
./moonhttp -h:moonip
```

#### 6. Definindo Host e Porta Personalizados
> Abre o servidor em um IP específico da máquina e em uma porta de sua escolha.

```bash
./moonhttp -h:192.168.0.50 -p:3000
```

#### 7. Ativando Navegação por Pastas (Directory Listing)
> Permite navegar pela estrutura de arquivos no navegador caso a pasta não possua um documento index.html.

```bash
./moonhttp --dir-list
```

#### 8. Comando Avançado (Rede Local + Porta Customizada + Listagem de Diretório)

Abre o servidor no IP local da máquina, em uma porta definida manualmente e com a listagem de arquivos liberada.

```bash
./moonhttp -h:moonip -p:9000 --dir-list
```

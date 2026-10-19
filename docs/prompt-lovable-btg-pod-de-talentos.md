# Prompt para o Lovable: BTG Pod de Talentos

Recrie com fidelidade total um protótipo navegável chamado **"BTG Pod de Talentos"**: um site de demonstração de um produto para recrutadoras em feiras de carreira. O candidato encosta o celular num pod NFC no estande da BTG Pactual, entra com o LinkedIn, escolhe o ano da faculdade e anexa o currículo. A recrutadora recebe o candidato no WhatsApp, avalia com um toque (Forte, Médio ou Fraco) e pode deixar uma observação. Depois do evento, ela revisa os melhores candidatos num painel.

Tudo é **front-end com dados de exemplo e simulações animadas**. Não há backend, login, banco de dados nem integração real com WhatsApp ou LinkedIn. Todo o texto da interface fica em **português do Brasil**, exatamente como escrito abaixo.

---

## 1. Tech stack

* React + TypeScript + Vite + Tailwind CSS (stack padrão do Lovable). shadcn/ui só se ajudar; o visual tem que seguir este documento, não o tema padrão.
* Sem backend e sem Supabase. Todo estado (candidatos, conversa, filtros) fica em memória com React state.
* Animações com CSS transitions/keyframes e `setTimeout` controlados (cancelar timers ao reiniciar ou trocar de aba). Pode usar Framer Motion se preferir, desde que os tempos sejam os descritos aqui.
* Fontes do Google Fonts:
  * **Familjen Grotesk** (500, 600, 700): títulos e display
  * **IBM Plex Sans** (400, 500, 600): texto
  * **IBM Plex Mono** (500, 600): rótulos em caixa alta, números e badges
* Uma única página com **3 abas** (sem rotas novas necessárias; se usar rotas, use `/`, `/candidato`, `/recrutadora`). Ao trocar de aba, rolar para o topo e lembrar a última aba no localStorage (com try/catch).
* Totalmente responsivo: tudo empilha em uma coluna abaixo de ~860px, com margem lateral mínima de 16px e sem rolagem horizontal.
* Suporte a tema claro e escuro via `prefers-color-scheme`.

## 2. Arquivos anexados (subir na pasta `public/` ou `src/assets/`)

| Arquivo | Onde usar |
|---|---|
| `btg-logo.png` | Logo quadrada da BTG (azul #001864 com "btg" branco). Barra do topo, cabeçalho da página do candidato no celular, notificação do iPhone, avatar do contato no WhatsApp |
| `pod-frente.jpg` | Foto do pod azul visto de frente. Imagem do hero da Visão geral e pod da simulação do candidato |
| `estande-pod.jpg` | Foto do candidato encostando o celular no pod no estande. Card 1 do "Como funciona" |
| `jose-leal.jpg` | Foto de perfil do candidato José Leal. Simulação do candidato, cartão no WhatsApp e painel |
| `tela-bloqueada.jpg` | Tela de bloqueio do iPhone (9:41). Tela inicial do celular na simulação |
| `btg-pod-talentos-video.mp4` | Vídeo demo de 45 s (1024x576, 16:9). Nova seção de vídeo na Visão geral |

## 3. Design system

### Cores (tema claro)
* Fundo da página `#f3f5f9` · superfície (cards) `#ffffff` · texto `#0b1733` · texto secundário `#5b6680` · bordas `#dfe4ee`
* **Azul BTG `#001864`** (barra do topo, hero, botões principais, aba ativa, chips selecionados) com texto branco; texto secundário sobre azul `rgba(255,255,255,.74)`
* Destaque/links `#0b3fb0` · fundo suave de destaque `#e6ecf8` · azul claro de eyebrow sobre azul `#9cc0ff`
* "Mesa" da simulação (fundo da foto do pod) `#efeded`
* Avaliações: Forte texto `#137a49` fundo `#def3e7` · Médio texto `#9a5f07` fundo `#fbefd9` · Fraco texto `#b4332a` fundo `#fbe3e0` · Pendente texto cinza com borda tracejada
* WhatsApp (claro): fundo da conversa `#efeae2`, balão recebido `#ffffff`, balão enviado `#d9fdd3`, cabeçalho `#008069`, texto `#111b21`, texto secundário `#667781`, botões/links `#027eb5`, divisórias `#e9edef`, botão enviar `#00a884`
* LinkedIn `#0a66c2`

### Tema escuro
Fundo `#0a0f1c`, superfície `#121a2c`, texto `#e8ecf4`, secundário `#9aa4ba`, bordas `#243049`, destaque `#9cc0ff`, fundo de destaque `#1a2747`. Forte `#5fd99a`/`#12301f`, Médio `#f2bd62`/`#33270f`, Fraco `#ff8a80`/`#3a1714`. WhatsApp escuro: fundo `#0b141a`, recebido `#202c33`, enviado `#005c4b`, cabeçalho `#1f2c34`, texto `#e9edef`, botões `#53bdeb`. A barra do topo, o hero e a "mesa" continuam iguais. As telas dentro do celular do candidato continuam claras.

### Tipografia e componentes
* Eyebrows: IBM Plex Mono 600, 12px, caixa alta, letter-spacing .08em, cor de destaque.
* Títulos: Familjen Grotesk 600/700, `text-wrap: balance`. H1 do hero `clamp(28px, 4.2vw, 44px)`, H2 de seção 24 a 28px.
* Cards: fundo branco, borda 1px `#dfe4ee`, raio 16px, padding 20px.
* Botões: raio 10px, padding 11x16, Plex Sans 600 14px. Variantes: azul (fundo #001864, texto branco), branco (fundo branco, texto azul), contorno (borda branca 40%), fantasma (fundo branco, borda cinza).
* Celulares: moldura preta `#0b0f1a` de 10px, raio 42px, ilha dinâmica preta (78x22px, raio 12px) centralizada no topo, sombra grande suave.

---

## 4. Estrutura geral

* **Barra do topo** azul #001864: logo BTG (34px, raio 8px) + texto **"Pod de Talentos"** (Familjen Grotesk 600 16px, branco). Nada mais.
* **Abas fixas (sticky)** logo abaixo, fundo branco, borda inferior: **"Visão geral"**, **"Visão do candidato"**, **"Visão da recrutadora"**. Aba ativa com texto escuro e sublinhado azul de 2px. A aba da recrutadora mostra um badge vermelho com "1" quando o candidato termina a simulação (some ao abrir a aba).
* Conteúdo centralizado, largura máxima 1120px.

---

## 5. Aba 1: Visão geral

### 5.1 Hero (bloco azul #001864, raio 20px, padding 36px, grid 2 colunas 1.15fr / .85fr)
* Eyebrow (azul claro #9cc0ff): **"FEIRAS DE CARREIRA"**
* H1 branco: **"Lembrar de cada candidato com um toque."**
* Parágrafo: **"O candidato encosta o celular no pod e deixa seus dados em 15 segundos. A recrutadora avalia no WhatsApp com um toque. Depois do evento, ela abre só quem importa."**
* Botões: **"Ver como o candidato usa"** (branco, vai para a aba 2) e **"Ver como a recrutadora usa"** (contorno, vai para a aba 3)
* Coluna direita: `pod-frente.jpg` num quadro com fundo #efeded e raio 16px.

### 5.2 Como funciona
* Eyebrow **"COMO FUNCIONA"** e H2 **"Três momentos, do estande ao processo seletivo"**
* 3 cards lado a lado (empilham no mobile). Cada card tem no topo uma **imagem 3:2 que ocupa toda a largura do card** (sem padding), e abaixo: círculo numerado (28px, fundo #e6ecf8, número em Plex Mono), rótulo em Plex Mono caixa alta, título e texto.

**Card 1**: imagem `estande-pod.jpg` (object-fit cover). Rótulo **"CANDIDATO · 15 SEGUNDOS"**, título **"Encosta o celular no pod"**, texto **"Abre uma página, entra com o LinkedIn, escolhe o ano e anexa o currículo. Recebe um número, por exemplo #1."**

**Card 2**: a "imagem" é uma mini tela de WhatsApp desenhada em HTML (fundo #efeae2). Construa o conteúdo num canvas fixo de 420x280px e escale com `transform: scale()` para caber inteiro no quadro 3:2, centralizado (recalcular no resize). Conteúdo:
  * Balão branco (78% da largura): **"Novo candidato · #1"** (negrito), **"José Leal"** (negrito maior), "University of South Florida · Sophomore", "Currículo anexado · " + link azul "linkedin.com/in/joseleal"
  * Botões estilo WhatsApp: **"✓ Forte"** (selecionado, fundo verde claro #d7f2ea) e **"Médio"** lado a lado, **"Fraco"** largura total
  * Balão branco: **"✅ Avaliação salva: José Leal · Forte"**
  * Rótulo **"RECRUTADORA · 1 TOQUE"**, título **"Avalia no WhatsApp"**, texto **"Chega um cartão com nome, faculdade, ano e LinkedIn. Ela toca em Forte, Médio ou Fraco e, se quiser, responde com uma observação que fica salva no perfil."**

**Card 3**: mini painel desenhado em HTML (fundo #f3f5f9), mesmo esquema de canvas 420x280 escalado. Conteúdo:
  * Chips de filtro: **"Forte 2"** (selecionado azul #001864), "Médio 1", "Fraco 1"
  * 3 linhas brancas com borda: foto `jose-leal.jpg` + **"José Leal"** / "USF · Sophomore" / "“Ótima comunicação, chamar para entrevista.”" + tag verde **Forte**; avatar "AR" (fundo #b4532a) + **"Ana Ribeiro"** / "UF · Junior" / "“Forte em valuation.”" + tag **Forte**; avatar "LP" (fundo #2e4a3a) + **"Lucas Prado"** / "NYU · Senior" + tag laranja **Médio**
  * Rótulo **"DEPOIS DO EVENTO"**, título **"Revisar os melhores candidatos"**, texto **"Um painel com foto, nota e currículo de cada um, filtrado por Forte e Médio, com o LinkedIn a um clique."**

### 5.3 NOVA SEÇÃO: vídeo (logo abaixo dos 3 cards)
* Um único bloco largo (largura total do conteúdo), proporção **16:9**, raio 20px, fundo azul #001864, borda 1px, sombra suave.
* Dentro, o vídeo `btg-pod-talentos-video.mp4` com `<video controls playsInline preload="metadata">`, `object-fit: contain`, preenchendo o bloco. Use como poster um frame do próprio vídeo ou `estande-pod.jpg`.
* Sem autoplay com som. Se quiser autoplay, apenas `muted` e `loop`, com os controles visíveis.
* Sem textos adicionais nesta seção, apenas o vídeo.

---

## 6. Aba 2: Visão do candidato (simulação automática)

Layout em 2 colunas (.9fr / 1.1fr). A coluna de texto fica sticky no desktop.

### 6.1 Coluna esquerda
* Eyebrow **"VISÃO DO CANDIDATO"**, H2 **"Do pod ao cadastro em 15 segundos"**
* Lista de 4 passos. O passo atual fica destacado (fundo branco, contorno, número em círculo azul #001864 com texto branco). Os concluídos ficam com o círculo em azul claro. Os futuros ficam com círculo de borda cinza.
  1. **"Encosta o celular no pod"** · "Sem app. O celular abre a página da BTG."
  2. **"Entra com o LinkedIn"** · "Foto, nome e faculdade aparecem sozinhos."
  3. **"Ano e currículo"** · "Escolhe o ano e, se quiser, anexa o currículo."
  4. **"Recebe o número"** · "A recrutadora recebe o cartão no WhatsApp na mesma hora."
* Nota com borda esquerda cinza: **"Quem não tem NFC usa o QR do pod. Quem não quer usar o LinkedIn digita o nome."**

### 6.2 Coluna direita: o palco
Palco de 640px de altura, raio 20px, fundo #efeded (a "mesa"), com:
* Texto no topo: **"Toque no botão para simular"** (some quando a simulação começa)
* `pod-frente.jpg` com `mix-blend-mode: multiply`, largura 300px, centralizado um pouco abaixo do meio
* Um iPhone (260x540px) **inclinado no canto superior direito** (rotate 14deg, scale .6), mostrando `tela-bloqueada.jpg` em tela cheia
* Barra de botões no rodapé: **"Encostar o celular no pod"** (azul). Durante e depois da simulação: **"Recomeçar"** (fantasma) e, no final, **"Ver o que a recrutadora recebeu"** (azul)

### 6.3 Sequência ao clicar em "Encostar o celular no pod"
Tudo acontece sozinho, com um **indicador de dedo** (círculo de 34px, azul translúcido com borda branca e sombra) que desliza até cada botão (transição de .6s), "pressiona" (scale .75 por ~150ms) e então aciona a ação. Os botões também funcionam com clique manual.

1. **Toque no pod (0s)**: o celular se move até o centro do pod, fica reto e reduz para scale .5 (transição .9s). Duas ondas azuis (#2f6fd6) se expandem do pod (anel que vai de scale .3 a 4 e some), em 0,55s e 0,85s.
2. **Notificação (1,3s)**: só agora aparece no topo do iPhone um **banner estilo iOS** (fundo branco 96%, raio 18px, sombra) descendo suavemente: logo BTG + **"BTG Pactual · Feira de Carreiras"** + "Toque para abrir". **Antes disso, nenhuma faixa ou banner pode aparecer no celular.**
3. **Abre a página (2,9s)**: o banner some, o celular volta ao centro em tamanho normal (scale 1) e o pod encolhe para o canto inferior esquerdo (150px). Passo 2 da lista fica ativo. A tela mostra:
   * Cabeçalho azul #001864 com logo BTG à esquerda e **"FEIRA DE CARREIRAS"** (Plex Mono, branco 80%) à direita
   * **"Prazer em te conhecer!"** e "Deixe seu contato para o time de recrutamento da BTG."
   * **Apenas** o botão azul do LinkedIn (#0a66c2) com ícone "in": **"Entrar com LinkedIn"**. Nada abaixo dele ainda.
4. **Clique no LinkedIn (+1,1s)**: o dedo vai até o botão e toca. O botão mostra um spinner e **"Conectando ao LinkedIn"** por 1,1s, depois fica verde claro (#e8f3ec, texto #137a49) com **"Conectado com LinkedIn"**.
5. **Perfil preenchido**: abaixo do botão, aparece animado (altura e opacidade) um card centralizado com a **foto `jose-leal.jpg` em círculo de 76px** (anel branco), **"José Leal"**, **"University of South Florida"** e o selo **"IMPORTADO DO LINKEDIN"** (Plex Mono, azul LinkedIn).
6. **Formulário (+0,7s)**: surge abaixo (passo 3 ativo):
   * **"Em que ano você está?"** com chips: Freshman, Sophomore, Junior, Senior (nenhum selecionado; selecionado = fundo #001864, texto branco)
   * **"Currículo (PDF)"**: área tracejada com ícone de clipe, **"Anexar currículo"** e "Opcional · você pode enviar depois" (input de arquivo real, aceita PDF)
   * Botão azul **"Enviar"**
   * Texto legal pequeno: **"Ao enviar, você autoriza a BTG Pactual a usar estes dados no processo seletivo, conforme a LGPD."**
   * A tela rola suavemente até o fim para mostrar o botão Enviar.
7. **Preenchimento automático**: dedo toca **Sophomore** (+1,3s); toca **Anexar currículo** (+2,6s), que vira **"Jose_Leal_Resume.pdf"** com "Anexado"; toca **Enviar** (+3,9s).
8. **Confirmação**: tela branca centralizada com check verde em círculo, **"CADASTRO RECEBIDO"** (Plex Mono), número grande **"#1"** (Plex Mono 64px, azul #001864), **"Obrigado, José!"** e "Foi um prazer te conhecer. O time de recrutamento da BTG já recebeu seu contato." Passo 4 ativo, badge "1" aparece na aba da recrutadora e surge o botão **"Ver o que a recrutadora recebeu"**.
9. **"Ver o que a recrutadora recebeu"** abre a aba 3 e **reinicia a demonstração do WhatsApp do zero**.
10. **"Recomeçar"** cancela todos os timers e volta ao estado inicial (celular inclinado com tela bloqueada).

---

## 7. Aba 3: Visão da recrutadora

Layout em 2 colunas (.8fr / 1.2fr).

### 7.1 Coluna esquerda
* Eyebrow **"VISÃO DA RECRUTADORA"**, H2 **"Avaliar sem largar a próxima conversa"**
* **iPhone com WhatsApp** (330x660px, mesma moldura):
  * Cabeçalho verde #008069: avatar redondo com a logo BTG, **"BTG · Pod de Talentos"** e "conta comercial"
  * Área da conversa com rolagem (sempre rola para a última mensagem)
  * Rodapé: campo arredondado "Mensagem" + botão redondo verde de enviar. Acima dele, uma barra de resposta (borda esquerda verde #06cf9c) que aparece ao responder uma mensagem: "Observação sobre [nome]" + botão "×" para cancelar.
* Card **"Como responder"** com dois itens (rótulo em Plex Mono num chip azul claro):
  * **Botão**: "Um toque em Forte, Médio ou Fraco. A avaliação já fica salva"
  * **Responder**: "Responda a pergunta de observação de qualquer candidato, na hora ou depois. Sem observação, é só ignorar"
  * Texto: **"Experimente: avalie o Pedro (#5) com um botão e depois toque em "Responder" na pergunta para deixar uma observação."**
  * Botão fantasma **"Repetir demonstração"**

### 7.2 Componentes do WhatsApp
* **Balão recebido**: branco, canto superior esquerdo reto, horário pequeno à direita embaixo.
* **Balão enviado**: verde claro, canto superior direito reto, horário + "✓✓" azul (#53bdeb). Quando é resposta, mostra no topo uma citação (borda esquerda verde, fundo levemente escuro) com "BTG · Pod de Talentos" em verde e a primeira linha da mensagem citada.
* **Separador** central: "HOJE".
* **Cartão de novo candidato** (formato de mensagem com botões de resposta rápida do WhatsApp Business):
  * Balão branco: **"Novo candidato · #N"** (negrito), nome em negrito maior, "Faculdade · Ano", "Currículo anexado" (se houver) e o link azul **"linkedin.com/in/[slug]"** (abre a busca do LinkedIn por nome + faculdade em nova aba), horário.
  * Abaixo do balão, botões brancos separados com sombra leve: **"Forte"** e **"Médio"** lado a lado, **"Fraco"** em largura total (texto azul #027eb5).
  * Ao tocar: o escolhido fica com **"✓ "** + fundo verde claro, os outros ficam desabilitados com opacidade .55. **Nenhuma mensagem é enviada pela recrutadora**, só o bot responde.
* **Depois de cada avaliação, o bot envia 2 mensagens**:
  1. **"✅ Avaliação salva: [Nome] · [Forte/Médio/Fraco]"**
  2. **"Tem alguma observação sobre o/a [primeiro nome]?\nResponda esta mensagem quando puder. Se não tiver, é só ignorar."** com um link azul **"Responder ↩"** ao lado do horário. Clicar nesse balão ativa a barra de resposta para aquele candidato.
* **Ao enviar uma observação** (respondendo, ou texto livre logo após avaliar): aparece o balão enviado (com citação, se for resposta) e o bot confirma **"📝 Observação salva no perfil de [Nome]."**. A observação aparece no painel.
* Texto "12 1" ou "12 forte" (número + avaliação) também avalia o candidato. Texto sem contexto recebe: "Use os botões de cada candidato para avaliar. Para uma observação, responda a pergunta do candidato."

### 7.3 Candidatos de exemplo
| # | Nome | Faculdade | Ano | Currículo | Slug LinkedIn | Foto/avatar | Horário |
|---|---|---|---|---|---|---|---|
| 1 | José Leal | University of South Florida | Sophomore | sim | joseleal | `jose-leal.jpg` | 13:45 |
| 2 | Ana Ribeiro | University of Florida | Junior | sim | ana-ribeiro-uf | avatar ilustrado (fundo #f3e3d8, cabelo longo castanho, blusa vinho) | 13:52 |
| 3 | Lucas Prado | New York University | Senior | sim | lucasprado | avatar ilustrado (fundo #dcefe4, cabelo cacheado preto, camisa verde escura) | 14:05 |
| 4 | Marina Costa | University of Pennsylvania | Sophomore | não | marina-costa-penn | avatar ilustrado (fundo #e6e3f6, cabelo longo loiro escuro, blusa azul) | 14:09 |
| 5 | Pedro Antunes | Columbia University | Junior | sim | pedroantunes | avatar ilustrado (fundo #e2ecf3, cabelo curto castanho, camisa cinza) | 14:18 |

Os avatares ilustrados são SVGs simples (fundo colorido, ombros, pescoço, rosto oval e cabelo) com tom de pele variado. Não usar fotos de pessoas reais além do José.

### 7.4 Demonstração automática do WhatsApp (roteiro exato)
Começa ao abrir a aba pela primeira vez, ao clicar em "Repetir demonstração" e ao vir do botão "Ver o que a recrutadora recebeu". Sempre zera a conversa e as avaliações antes. Ritmo lento para dar tempo de ler: **1,6s** após cada mensagem comum, **2,6s** após cada cartão de candidato, **2,8s** após cada avaliação e cada observação.

1. Separador **"HOJE"**
2. Enviada (13:40): **"Começar"**
3. Bot (13:40): **"Olá! Vamos ativar uma nova feira de carreiras.\nQual é o nome do evento?"**
4. Enviada (13:41): **"Brasa Summit Americas 2026"**
5. Bot (13:41): **"✅ Evento salvo: Brasa Summit Americas 2026.\nA partir de agora, cada candidato que encostar no pod aparece aqui."**
6. Cartão **#1 José Leal** (13:45) → toque em **Forte** (13:46) → bot confirma e pergunta → resposta citada (13:47): **"Ótima comunicação, entende bem de mercado financeiro e fintech. Chamar para entrevista."** → bot: "📝 Observação salva no perfil de José Leal."
7. Cartão **#2 Ana Ribeiro** (13:52) → **Forte** (13:53) → resposta citada (13:54): **"Forte em valuation, explicou bem o case de crédito. Chamar para entrevista."** → confirmação
8. Cartão **#3 Lucas Prado** (14:05) → **Médio** (14:06) → (ela ignora a pergunta por enquanto)
9. Cartão **#4 Marina Costa** (14:09) → **Fraco** (14:10) → (ignora por enquanto)
10. Ela volta e responde a pergunta do Lucas (14:12): **"Comunicação boa, pouca base técnica. Rever depois do case."** → confirmação
11. Responde a pergunta da Marina (14:13): **"Pouco preparada sobre a BTG e sem interesse claro na área."** → confirmação
12. Cartão **#5 Pedro Antunes** (14:18) fica **pendente**, com botões ativos, para o visitante testar.

### 7.5 Coluna direita: painel pós evento
* Eyebrow **"PAINEL PÓS EVENTO"**, H2 **"Brasa Summit Americas 2026"**, à direita "Candidatos de exemplo" (pequeno, cinza)
* Chips de filtro com contagem: **Todos, Forte, Médio, Fraco, Pendente** (ativo = fundo #001864, texto branco). Contagens em Plex Mono.
* Lista em card com divisórias. Cada linha: foto/avatar 46px (raio 12px), **nome** + "#N" (Plex Mono), "Faculdade · Ano · horário · currículo", observações em caixas cinza claras, e à direita a tag de avaliação (Forte/Médio/Fraco/Pendente) e o link **"Abrir no LinkedIn"** (busca `https://www.linkedin.com/search/results/people/?keywords=` + nome + faculdade, nova aba).
* Ordenação: Forte, Médio, Fraco, Pendente; dentro do grupo, número maior primeiro.
* Atualiza em tempo real junto com a conversa. Linha alterada pisca levemente em azul claro (1,6s).
* Filtro vazio: "Nenhum candidato neste filtro."
* Texto abaixo: **"Cada perfil guarda foto, faculdade, ano, currículo, avaliação e notas. "Abrir no LinkedIn" busca o candidato pelo nome e pela faculdade."**

---

## 8. Regras finais
* Fidelidade total aos textos, cores, tempos e ordem descritos. Não inventar seções, não adicionar rodapé, depoimentos, preços ou formulários extras.
* A única seção nova em relação ao protótipo original é o **bloco do vídeo** abaixo dos 3 cards do "Como funciona".
* Nada de em dash nos textos.
* Respeitar `prefers-reduced-motion` (animações quase instantâneas).
* Testar em desktop e em celular (400px de largura): os celulares simulados não podem estourar a tela, e as mini telas dos cards 2 e 3 devem caber inteiras no quadro 3:2.

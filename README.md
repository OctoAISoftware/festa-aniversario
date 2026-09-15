# Festa da Maia — Organizador de Festa de Aniversário

Jogo infantil, browser-only, três telas, totalmente sem dependências externas de JS.

- **Tela 1 — Montar a festa**: criança toca nos 40 itens do catálogo para montar o checklist da festa. Campo opcional para o nome do(a) aniversariante (também aceita `?nome=…` na URL).
- **Tela 2 — Achar os itens**: cena kawaii com os itens selecionados espalhados em **fases de 6 a 10 alvos**; cada fase tem transição festiva, indicador visual de progresso (estrelinhas), dica automática quando um item fica coberto. Toque ou teclado (Enter/Espaço) marcam cada item.
- **Tela 3 — A festa está pronta**: cena final montada com todos os itens escolhidos (bolo, velinhas, decoração de parede, balões, piñata, presentes, etc.), confete, mascotes (coelha rosa + ursinho amarelo), banner "Parabéns pra [nome]!" e botões para reiniciar ou variar.

Estilo visual é kawaii **original** — pastel pinks, laços, corações, brilhos, mascotes desenhados do zero em SVG inline. **Nenhum personagem Sanrio / Hello Kitty** é usado.

## Stack

- HTML + CSS + JS vanilla. Zero build, zero dependências.
- Ícones dos 40 itens: SVG baixado do banco de imagens gratuito **[OpenMoji](https://openmoji.org/)** (licença CC BY-SA 4.0 — crédito no rodapé do site).
- **11 ilustrações kawaii originais** desenhadas para esta versão (em estilo pastel, traço coeso com o resto do set): **piñata, guardanapos, toalha, bandeirolas, luzinhas, decoração de parede, fantasia, máscara de festa, boca do palhaço, tatuagem de festa, balões (buquê)**. Substituem os glifos OpenMoji que não honravam o nome do item. **Convite** é o novo item que substituiu `bexiga` (mesmo conceito de "balões") para manter o roster em 40.
- Mascotes (coelha + urso), cena do bolo, decoração, banner, chamas das velas: arte original feita para a Maia em SVG inline.
- WebAudio sintetizado para feedback (acorde maior ao acertar, arpejo ao completar fase, ruído filtrado para aplauso na cena final). AudioContext inicializa no primeiro gesto do usuário (autoplay policy).
- Hospedagem: este app é estático (`python3 -m http.server` na porta alocada).

## Deploy

Aplicação já publicada:

- **URL pública:** https://festa-aniversario.octoaisoftware.com/
- **Porta local:** 8361
- **pm2 app:** `site-festa-aniversario-okto` (e `-tunnel` para o connector Cloudflare)
- **Tunnel:** named tunnel `site-festa-aniversario-okto` → `*.octoaisoftware.com`
- **Diretório durável:** `/home/guardiao/sites/festa-Maia/`

Para re-deployar:

```bash
OCKY_SITE_PORT_RANGE="8361-8399" \
TUNNEL_DOMAIN="octoaisoftware.com" \
CF_ACCOUNT_ID="..." CF_ZONE_ID="..." CF_API_TOKEN="..." INSTANCE_KEY="okto" \
bash /home/guardiao/mitosis-okto/mitosis/host-site.sh "festa-aniversario" \
"/home/guardiao/sites/festa-Maia" "static"
```

Deploy desta versão: editar os arquivos in-place + `git commit && git push` no repo (sem restart do `pm2`).

## Mobile-first

- Sem scroll horizontal em nenhum breakpoint testado (320 → 1280 px).
- Tiles com tamanho mínimo de 103×118 px e ícones 64 px (alvo de toque ≥ 48 px).
- CTA 260×59 px com `safe-area-inset-bottom` e `position: sticky` — sempre visível no mobile.
- Grid responsivo: 3 colunas em phones pequenos, 4–5 em phones grandes, 5–6 em tablet/desktop.
- Sidebar do checklist retorna em ≥ 760 px.
- **Tela "Achar os itens"**: sprites de 168 unidades de viewBox com hit-area de 196 (±85–193 CSS px conforme viewport). Items espalhados por grid-search determinístico que maximiza distância ao vizinho mais próximo + bônus para a metade direita; variação determinística de escala (0.85–1.30) e rotação (-30°…+30°) por item; z-order embaralhado por jogo; o hint timer faz wiggle + sparkles em qualquer alvo coberto **e** semi-translada os itens não-encontrados quando necessário para que o item coberto apareça.

## Lista dos 40 itens

bolo, velinhas, docinhos, brigadeiro, sorvete, refrigerante, suco, salgadinhos, pizza, pipoca, algodão-doce, cachorro-quente, balões, chapéu de festa, serpentina, confete, pratinhos, copos, guardanapos, toalha da mesa, bandeirolas, luzinhas, decoração de parede, música, presente, sacola de lembrancinhas, máscara de festa, fantasia, guarda-chuva de papel, piñata, dança das cadeiras, pescaria, corrida do saco, boca do palhaço, amarelinha, tatuagem de festa, convite, palhaço, coroa, colar de guloseimas.

## Cena final

- **Bolo + velinhas sempre sobre o bolo** (velas apoiadas, sem flutuar).
- **Mascotes reduzidos (scale 0.78)** e reposicionados nas laterais (coelha à esquerda, urso à direita) para não dominarem a composição.
- **Balões** reposicionados para a área superior-direita (sem colidir com mascotes).
- **Itens selecionados** vão automaticamente para um grid 5×N no centro da mesa, com escala 0.6 e células 80×60 px — colisão real detectada pelo grid, sem sobreposição.
- **Luzinhas, bandeirolas, mesa** só aparecem se o respectivo item foi escolhido.
- **Paleta usa os 5 tiers** (não só 3 como antes) — `finalSeed % 5`.
- **"Outra variação"** re-embaalha os placements (com novo seed) além de mudar cores.

## Acessibilidade

- `aria-live` no contador; `role="button"` + `tabindex="0"` + `aria-label` em cada alvo da caça.
- Foco visível em todos os controles interativos (outline rosa + drop-shadow).
- Ativação por teclado: Enter ou Espaço disparam o `findItem` do alvo focado.
- `aria-pressed` é resetado no restart junto com o estado visual.
- `viewport` sem `user-scalable=no` — zoom do navegador permitido.
- Respeita `prefers-reduced-motion`.

## UX

- Mobile-first, touch-friendly, alvo mínimo ≥ 48 px.
- Funciona em paisagem e retrato.
- Animações em todas as transições de estado (respeita `prefers-reduced-motion`).
- Splash de carregamento curtinho (~600 ms) com mascote saltitante.
- Feedback sonoro sintetizado (WebAudio): acorde maior ao acertar, arpejo ao completar fase, aplauso na cena final.
- Hint anti-frustração: a cada ~5s sem encontrar, prioriza os itens mais cobertos (por z-order) para wiggle + sparkles; se ainda estiverem muito cobertos, semi-translada os outros para deixar o alvo aparecer.
- Confete nasce exatamente no ponto do acerto (container 0×0 no hit-point).
- Toque em área vazia só gera um pufzinho neutro — nada de erro / som agressivo.
- Banner final personaliza com o nome do(a) aniversariante (input ou `?nome=…`, sanitizado).

## Testes automatizados

```bash
node /tmp/opencode/test-festa.js
```

Cobre 4 cenários (mobile-6, mobile-40, desktop-6, desktop-40) com 19 verificações cada:
seleção mínima, contador, geração de fases, ausência de alvo totalmente encoberto, distribuição uniforme pelo stage (inclusive metade direita), hit-area ≥ 48 px, transição completa até o finale, todos os itens selecionados aparecem na cena final, confete nasce no alvo, velas apoiadas no bolo, banner com nome configurado, restart limpa estado visual + `aria-pressed`, zoom permitido, alvo recebe foco, sem erros de console, sem 404s.

## Créditos

- Ícones da maioria dos itens cortesia do [OpenMoji](https://openmoji.org/), licença [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
- **Ilustrações originais kawaii** feitas para a Maia: algodão-doce, chapéu de festa, sacola, saco (corrida), colar, tatuagem, amarelinha, convites, mascotes (coelha + urso), cena do bolo, decoração, chamas das vela, **piñata, guardanapos, toalha, bandeirolas, luzinhas, decoração de parede, fantasia, máscara de festa, boca do palhaço, buquê de balões** — redesenhados para que o glifo honre o nome do item (vários dos OpenMoji estavam errados: a "piñata" parecia caça-níquel, o "balão" parecia cereja, a "máscara" parecia rosto de super-herói, a "boca do palhaço" parecia rosto com chapéu de caubói, etc.).
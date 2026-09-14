# Festa da Maia — Organizador de Festa de Aniversário

Jogo infantil, browser-only, três telas, totalmente sem dependências externas de JS.

- **Tela 1 — Montar a festa**: criança toca nos 40 itens do catálogo para montar o checklist da festa.
- **Tela 2 — Achar os itens**: cena kawaii com todos os itens selecionados espalhados; criança toca em cada um para marcar.
- **Tela 3 — A festa está pronta**: cena final montada com os itens que ela escolheu, confete, mascotes e botões para jogar de novo.

Estilo visual é kawaii **original** — pastel pinks, laços, corações, brilhos, mascotes (coelha com laço rosa e ursinho amarelo) desenhados do zero em SVG inline. **Nenhum personagem Sanrio / Hello Kitty** é usado.

## Stack

- HTML + CSS + JS vanilla. Zero build, zero dependências.
- Ícones dos 40 itens: SVG baixado do banco de imagens gratuito **[OpenMoji](https://openmoji.org/)** (licença CC BY-SA 4.0 — crédito no rodapé do site).
- Mascotes (coelha + urso), cena do bolo, decoração, banner: arte original feita para a Maia em SVG inline.
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

## Mobile-first

A camada visual foi refeita com mobile-first verdadeiro:
- Sem scroll horizontal em nenhum breakpoint testado (320 → 1280 px).
- Tiles com tamanho mínimo de 103×118 px e ícones 64 px (alvo de toque ≥ 48 px).
- CTA 260×59 px com `safe-area-inset-bottom`.
- Grid responsivo: 3 colunas em phones pequenos, 4–5 em phones grandes, 5–6 em tablet/desktop.
- Sidebar do checklist retorna em ≥ 760 px.

## Lista dos 40 itens

bolo, velinhas, docinhos, brigadeiro, sorvete, refrigerante, suco, salgadinhos, pizza, pipoca, algodão-doce, cachorro-quente, balões, chapéu de festa, serpentina, confete, pratinhos, copos, guardanapos, toalha da mesa, bandeirolas, luzinhas, decoração de parede, música, presente, sacola de lembrancinhas, máscara de festa, fantasia, guarda-chuva de papel, piñata, dança das cadeiras, pescaria, corrida do saco, boca do palhaço, amarelinha, tatuagem de festa, bexiga, palhaço, coroa, colar de guloseimas.

## UX

- Mobile-first, touch-friendly, alvo mínimo ≥ 48 px.
- Funciona em paisagem e retrato.
- Animações em todas as transições de estado (respeita `prefers-reduced-motion`).
- Splash de carregamento curtinho (~600 ms) com mascote saltitante.
- Sem áudio dependente (apenas feedback visual).
- Hint anti-frustração: após ~10 s sem encontrar, o próximo item escondido faz wiggle + sparkles.
- Toque em área vazia só gera um pufzinho neutro — nada de erro / som agressivo.

## Créditos

Ícones dos 40 itens cortesia do [OpenMoji](https://openmoji.org/), licença [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
Mascotes e cena final: arte original feita para a Maia.
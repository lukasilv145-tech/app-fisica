# ⚛️ LabFísica — Simulador de Experimentos de Física

Web app educacional que simula **19 experimentos interativos** cobrindo os conteúdos de **Física 1 (Mecânica), Física 2 (Termodinâmica, Fluidos e Ondas) e Física 3 (Eletromagnetismo)** — tudo rodando 100% no navegador, hospedado de graça no **Google Apps Script**.

Cada experimento tem animação em canvas, parâmetros ajustáveis por sliders e medições calculadas em tempo real a partir das equações físicas reais (nada de valores fake).

---

## 🧪 Experimentos disponíveis

### Física 1 — Mecânica
| Experimento | O que simula | Conceitos |
|---|---|---|
| Lançamento Oblíquo | Projétil com trajetória prevista e vetores vx/vy | Cinemática 2D, alcance, altura máxima |
| Plano Inclinado com Atrito | Bloco deslizando (ou não) na rampa | Decomposição de forças, atrito estático × cinético |
| Colisões 1D | Dois blocos com coeficiente de restituição ajustável | Conservação de momento, colisão elástica/inelástica |
| Pêndulo Simples | Equação real θ̈ = −(g/L)·senθ, sem aproximação | MHS, período independente da massa |
| Massa-Mola (MHS) | Oscilador com barras de energia ao vivo | ω = √(k/m), conservação de energia |

### Física 2 — Termo, Fluidos e Ondas
| Experimento | O que simula | Conceitos |
|---|---|---|
| Gás Ideal no Pistão | Partículas acelerando com T, pistão móvel | PV = nRT, teoria cinética |
| 1ª Lei da Termodinâmica | Diagrama PV interativo com 4 transformações | ΔU = Q − W, isotérmica/isobárica/isocórica/adiabática |
| Máquina Térmica | Volante, reservatórios e fluxos de energia | η = W/Qh, limite de Carnot, violação da 2ª lei |
| 2ª Lei — Entropia | Calor fluindo quente → frio com medidor de ΔS | dS ≥ 0, irreversibilidade |
| Propagação do Som | Onda longitudinal com compressões visíveis | v = λf, velocidade em ar/água/aço |
| Superposição de Ondas | Duas ondas somando na mesma corda | Batimentos, interferência, ondas estacionárias |
| Pressão Hidrostática | Sensor de profundidade em tanque | Lei de Stevin: P = P₀ + ρgh |
| Hidrodinâmica | Tubo de Venturi com manômetros | Continuidade A₁v₁ = A₂v₂ e Bernoulli |
| Empuxo | Objeto que flutua ou afunda conforme densidade | Princípio de Arquimedes |
| Calorimetria | Dois corpos indo ao equilíbrio térmico | Teq, Q cedido = Q recebido |

### Física 3 — Eletromagnetismo
| Experimento | O que simula | Conceitos |
|---|---|---|
| Campo Elétrico | Grade de vetores E de duas cargas | Lei de Coulomb, dipolo |
| Campo Magnético — Fios Paralelos | Vetores B, linhas circulares, força entre fios | B = μ₀I/2πr, regra da mão direita, F/L |
| Circuito RC | Curva de carga/descarga com marcações de τ | V(t) = V₀(1 − e^(−t/τ)), constante de tempo |
| Carga em Campo Magnético | Movimento circular com vetores v e F | F = qvB, raio ciclotrônico |

---

## 📏 Sistema de unidades

O painel lateral tem um card **Unidades** com seletores para:

- **Velocidade:** m/s · km/h · ft/s
- **Temperatura:** K · °C · °F
- **Pressão:** kPa · atm · mmHg · psi
- **Distância:** m · cm · km
- **Energia:** J · kJ · cal
- **Força:** N · kgf · dyn

A física interna roda **sempre em SI** — a conversão acontece apenas na camada de exibição das medições, então trocar de unidade nunca altera o resultado da simulação.

---

## 🕹️ Como usar

- **Abas** no topo alternam entre Física 1, 2 e 3 (cada módulo tem sua cor).
- **Chips** logo abaixo selecionam o experimento do módulo.
- **Sliders** no painel lateral ajustam os parâmetros — alguns aplicam ao vivo, outros reiniciam a simulação.
- **Medições** mostram as grandezas calculadas em tempo real.
- **⏸ pausar / ↻ reiniciar** ficam no cabeçalho do palco.
- A descrição abaixo do canvas traz a equação principal de cada experimento.

---

## 🏗️ Arquitetura

```
Code.gs      → doGet() servindo o app via HtmlService (backend inteiro em 10 linhas)
Index.html   → app completo: CSS + HTML + engine JS + 19 simulações
```

**Engine:** um único loop de `requestAnimationFrame` chama `update(S, dt)` e `draw(ctx, S)` da simulação ativa. Cada experimento é um objeto no registro `SIMS` com o contrato:

```js
SIMS.exemplo = {
  title: '...', mod: 'f1|f2|f3',
  desc: 'texto com as equações',
  controls: [ { id, label, min, max, step, value, unit, live } ],
  init(S)        { /* estado inicial */ },
  update(S, dt)  { /* passo de física */ },
  draw(ctx, S)   { /* renderização no canvas */ },
  read(S)        { return [ { k: 'grandeza', v: 'valor' } ]; }
};
```

Sliders com `live: true` aplicam sem resetar; os demais reiniciam a simulação ao mudar. Os parâmetros ficam em `S.p`, o resto do estado vive em `S`.

**Adicionar um experimento novo** = criar o objeto no `SIMS` + incluir o id no array do módulo em `MODULES`. Nada mais.

---

## 🔧 Stack

- **Google Apps Script** (HtmlService) — hospedagem gratuita, zero servidor
- **Canvas 2D API** — todas as animações, sem bibliotecas externas
- **JavaScript vanilla** — sem frameworks, sem build, um arquivo só
- **Google Fonts** — Space Grotesk (display) + IBM Plex Mono (dados)

---

## 📌 Notas e limitações

- As simulações usam integração numérica simples (Euler semi-implícito) — precisa o suficiente pra fins didáticos, não pra pesquisa.
- Algumas escalas são visuais (ex.: raio no ciclotron, pixels no campo elétrico) pra caber na tela; as **medições** seguem as fórmulas reais.
- A animação do som roda em câmera lenta (~100×), senão seria impossível enxergar a oscilação.
- Testado em Chrome/Edge/Firefox desktop e mobile (layout responsivo abaixo de 900 px).

---

## 🗺️ Roadmap (ideias futuras)

- [ ] Lei de Ohm com circuito montável (série/paralelo)
- [ ] Indução eletromagnética (Faraday/Lenz)
- [ ] Ciclo de Carnot completo animado no diagrama PV
- [ ] Óptica geométrica (lentes e espelhos) — Física 4 quem sabe 👀
- [ ] Exportar gráfico/medições como imagem

---

Feito com ☕ e `requestAnimationFrame`.

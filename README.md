# RefPro SILVER (VST3 · Windows · REAPER)

Plugin de análise de equilíbrio tonal inspirado no iZotope Tonal Balance Control 3.
O áudio passa intacto; o plugin só mede.

## Recursos
- Interface em tema **prata** com texto azul-marinho (painel de alvos e controles do EQ dinâmico na barra lateral, gráfico com regiões Low / Low-Mid / High-Mid / High, medidores embaixo)
- Curva de referência **All-Purpose** com faixa de tolerância
- Medidores de Análise: **Vocal Balance**, **Dynamics**, **Stereo Width**
- **Equalizador de 10 bandas** (Bell, Low/High Shelf, Low/High Cut, Notch): dois cliques no gráfico criam uma banda, arraste o nó (freq/ganho), roda do mouse muda o Q, clique direito abre o menu. Botão *Equalizer* liga/desliga; na engrenagem, escolha analisar Pre-EQ ou Post-EQ
- **EQ dinâmico automático (botão Dynamic)**: 31 bandas de 1/3 de oitava que seguem a curva de referência, cortando o que passa da tolerância e **acentuando o que está abaixo dela**. Sem latência. Ordem do sinal: EQ estático → EQ dinâmico
  - *Amount* (% do excesso corrigido), *Max Cut*, *Max Boost* (padrão +3 dB), *Tolerance* (folga extra), *Speed* (média de 1,5 / 3 / 6 s)
  - *Adapt* (0-100%): 0 = curva fixa. Quanto maior, mais a referência absorve o caráter **amplo** da música (inclinação/brilho geral, ~2,7 oitavas) e só desvios estreitos (picos e buracos) são corrigidos. A referência adaptada aparece tracejada no gráfico
  - A âncora de nível usa a **mediana** dos desvios (um pico isolado não desloca a referência inteira)
  - Piso de ruído: não acentua bandas com quase nenhum sinal
  - *Level Comp*: ganho automático (até ±6 dB) que mantém o volume geral estável
  - Display: faixa "Dynamic EQ (dB)" com cortes para baixo (laranja) e boosts para cima (azul-esverdeado), mais a curva do ganho total em laranja. Os limites de frequência (Dynamic Low/High Limit) ficam nos parâmetros do plugin no REAPER
- **Auto ±2 dB (Vocal Balance)**: botão sob o medidor. Quando o nível da voz sai da faixa ótima (±1,5 dB do alvo), aplica até ±2 dB de correção no canal **centro (mid)** da região vocal (~300 Hz-3,5 kHz), com ataque lento (1,5 s) e release de 3 s. Só atua quando há voz detectada. A medição vem do detector antes da correção (sem realimentação). O limite (*Vocal Auto Max*) é um parâmetro do plugin. Ordem do sinal: EQ estático -> EQ dinâmico -> ajuste da voz
- **Vocal Balance** heurístico (sem IA): considera só o que está no centro estéreo, com espectro tonal e modulação silábica (1,2-8 Hz), comparando a voz com o resto da mix. Mostra "No vocal detected" quando não encontra voz
- **Capture**: salva a curva média atual como novo alvo (até 8, gravados no projeto)
- Visualização em curva ou barras, médias de 3 s / 10 s / 30 s / música inteira (menu da engrenagem)
- Extra Meters: curva rápida + valores numéricos em dB

## Compilar no GitHub
1. Crie um repositório e envie todo o conteúdo desta pasta (inclusive `.github/`).
2. Abra a aba **Actions** → *Build RefPro SILVER* (roda a cada push, ou use *Run workflow*).
3. Ao terminar, baixe o artefato **RefPro-SILVER-VST3-Windows**.

## Instalar no REAPER
1. Copie a pasta `RefPro SILVER.vst3` para `C:\Program Files\Common Files\VST3\`.
2. REAPER → Options → Preferences → Plug-ins → VST → **Re-scan**.
3. Coloque o plugin no **Master** (ou no canal a analisar), por último na cadeia.

## Ajustes
- Curva All-Purpose: `Source/Targets.h` (pontos freq / dB / tolerância).
- Alvos de Dynamics (PSR) e Stereo Width: campos `psr` e `widthDb` do mesmo arquivo.

## Observações
- A curva All-Purpose é uma aproximação original, não são dados da iZotope. Para referências reais, toque uma faixa que você admira e use **Capture**.
- Não inclui: EQ embutido, app standalone, Target Blender, controle remoto de outros EQs.
- Vocal Balance, Dynamics e Stereo Width são estimativas próprias, não os algoritmos da iZotope. Vocal Balance: nível da voz (centro + tonal + silábico) vs. resto da mix; Dynamics: pico ÷ RMS de 3 s; Stereo Width: energia Side ÷ Mid.
- O ajuste automático da voz age no centro estéreo inteiro naquela faixa (voz, mas também caixa, base, etc.); não separa a voz como um stem. Se a voz estiver muito baixa para ser detectada, ele não atua.
- Limite do Vocal Balance: instrumento tonal exatamente no centro com notas bem separadas pode ser confundido com voz. O alvo padrão (-6 dB) é ajustável em `Source/Targets.h` (`vocalDb`); o **Capture** grava o valor medido numa faixa de referência.
- O EQ dinâmico usa um detector próprio (média de 1,5 / 3 / 6 s) que mede o sinal antes do corte, para não corrigir a própria correção. Sem latência. Se soar "vivo" demais ou "achatado", ajuste Amount e Max Cut.
- O EQ, o EQ dinâmico e o novo Vocal Balance foram testados só com sinais sintéticos e compilação no Linux; teste no REAPER antes de usar em trabalho final.

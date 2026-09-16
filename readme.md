# 🍣 Sushi Scanner — Protótipo

Protótipo de demonstração para um restaurante japonês que cobra por peça de sushi.
A ideia: o cliente monta o prato, o atendente aponta uma câmera para ele, o sistema
identifica automaticamente quantas peças de cada tipo existem e calcula o valor final —
sem precisar contar manualmente.

> ⚠️ Este é um **protótipo de demonstração**, não um produto pronto para produção.
> Vários pontos ainda são simulados (veja a seção [Estado atual](#estado-atual-o-que-é-real-e-o-que-é-simulado)).

---

## Como funciona (visão geral)

```
Câmera do atendente
      ↓
Foto do prato inteiro
      ↓
Separar cada peça individualmente   (ainda não implementado neste protótipo)
      ↓
Classificar cada peça separada      (niguiri? hossomaki? uramaki?)
      ↓
Contar peças por tipo + somar preços
      ↓
Mostrar valor final na tela
```

---

## Por que cada tecnologia foi escolhida

Um ponto importante logo de cara: **nada aqui usa IA generativa (LLM/GPT)**. O projeto
usa *Machine Learning clássico* — um classificador de imagem treinado especificamente
para reconhecer as peças de sushi do restaurante. É uma técnica bem mais simples,
mais leve e mais previsível que um modelo de linguagem, e roda inteira no navegador,
sem depender de nenhuma API paga.

| Tecnologia | Papel no projeto | Por que essa escolha |
|---|---|---|
| **HTML/CSS/JS puro** | Interface e lógica geral | Protótipo precisa ser simples de rodar e mostrar — sem processo de build, sem instalar nada, basta abrir no navegador. |
| **`getUserMedia` (API nativa do navegador)** | Ligar a câmera e capturar a foto do prato | Já vem no navegador, gratuito, sem biblioteca externa necessária. |
| **TensorFlow.js** | Motor que roda o modelo de Machine Learning no navegador | Permite treinar e/ou executar modelos de reconhecimento de imagem 100% no client-side, sem servidor. |
| **ml5.js** | Camada simplificada sobre o TensorFlow.js | Reduz bastante a quantidade de código necessária para treinar/rodar um classificador de imagem, sem precisar programar as camadas da rede neural manualmente. |
| **CVAT** (ferramenta externa, não é código) | Organizar e/ou anotar as fotos usadas no treino do modelo | Open-source e gratuito, sem limite de créditos (diferente de algumas plataformas comerciais). Usado *fora* deste repositório, na etapa de preparar os dados de treino. |
| **Google Teachable Machine** (opcional) | Atalho para treinar um classificador simples sem escrever código de treino | Mantido como plano B: se o treino manual via TensorFlow.js/ml5.js tomar muito tempo, o Teachable Machine treina um modelo utilizável em minutos e exporta pronto para rodar neste projeto. |
| **JS puro (`pricing.js`)** | Regra de negócio: preço por peça e cálculo do total | Não precisa de nenhuma biblioteca — é só uma tabela de preços e uma soma. Mantido isolado do resto do código de propósito, para ser fácil de editar sem mexer na parte de Machine Learning. |

---

## Estrutura do projeto (ponto de partida)

```
sushi-scanner/
├── package.json     # Dependências do projeto (TensorFlow.js, ml5.js)
├── .gitignore
└── README.md
```

Por enquanto o repositório só tem o `package.json` com as dependências definidas e
este README. O código (câmera, classificador, cálculo de preço, interface) ainda
será escrito — este é o ponto de partida do projeto, não a implementação completa.

---

## Dependências definidas no `package.json`

| Pacote | Papel |
|---|---|
| `@tensorflow/tfjs` | Motor que roda (e pode treinar) modelos de Machine Learning no navegador. |
| `ml5` | Camada simplificada sobre o TensorFlow.js, facilita treinar/rodar um classificador de imagem. |
| `serve` (dev) | Servidor local simples, só para rodar o protótipo no navegador durante o desenvolvimento (a câmera exige um servidor, não pode ser aberto direto como arquivo). |

Para instalar:

```bash
npm install
```

---

## Próximos passos

1. **Coletar fotos reais** das peças do restaurante (várias fotos por categoria,
   ângulos e iluminações diferentes).
2. **Organizar/anotar as fotos no CVAT** (ou simplesmente separar em pastas por
   categoria, se for usar classificação simples em vez de detecção).
3. **Treinar o modelo**:
   - Caminho rápido: Google Teachable Machine (exporta modelo pronto para
     TensorFlow.js).
   - Caminho com mais controle: treinar manualmente com TensorFlow.js/ml5.js.
4. **Escrever o código do protótipo**: captura de câmera (`getUserMedia`),
   carregamento do modelo treinado, tabela de preços e interface.
5. **Resolver a separação de peças na foto do prato** — se o prato tiver várias
   peças diferentes na mesma imagem, será necessário detectar cada uma
   individualmente (ex: com OpenCV.js) antes de classificar.

---

## Perguntas em aberto (para decidir com o cliente)

- As peças que precisam ser diferenciadas têm diferença visual perceptível
  a olho nu, de cima? (ex: niguiri de salmão vs. atum, uramaki com ou sem kani)
  Se a diferença for só no recheio interno, nenhuma câmera vai conseguir captar isso.
- O prato terá as peças bem separadas entre si, ou podem ficar encostadas/sobrepostas?
  Isso afeta diretamente a dificuldade de separar cada peça na foto.

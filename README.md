# Detector de Carta — Teachable Machine

Aplicação web simples que usa a webcam para identificar, em tempo real, se o usuário está segurando uma carta ou não. O modelo de reconhecimento de imagem foi treinado no [Teachable Machine](https://teachablemachine.withgoogle.com/) do Google e é consumido no navegador com TensorFlow.js.

## Sobre o modelo

- **Tipo:** Classificação de imagem (Image Project)
- **Classes:**
  - `com carta` — usuário segurando uma carta em frente à câmera
  - `sem carta` — usuário sem nenhuma carta em frente à câmera
  - `fundo` — apenas o ambiente/fundo, sem o usuário no centro da cena
- **Entrada:** frames da webcam (224x224px)
- **Saída:** probabilidade de cada classe, atualizada em tempo real a cada frame

## Modelo

O modelo não é carregado de um link externo do Teachable Machine — ele foi exportado como arquivo (painel *Export Model → Download my model*) e está incluído neste próprio repositório, na pasta [`my_model/`](./my_model):

```
my_model/
 ├── model.json      → arquitetura do modelo
 ├── metadata.json   → labels e metadados
 └── weights.bin     → pesos treinados
```

O `index.html` carrega o modelo direto dessa pasta local:

```js
const URL = "./my_model/";
```

> Se você preferir usar o link hospedado pelo Google em vez dos arquivos locais, basta trocar essa constante pelo link do painel *Export Model → Upload my model* (formato `https://teachablemachine.withgoogle.com/models/hf0yu45t0/`).

## Código-fonte da aplicação

Todo o código (HTML, CSS e JavaScript) está em um único arquivo: [`index.html`](./index.html).

Estrutura resumida:

```
index.html
 ├── <head>   → estilos (tema roxo, cards, botão, layout responsivo)
 ├── <body>   → botão "Iniciar câmera", área da webcam e lista de labels
 └── <script> → carregamento do modelo (tmImage.load), captura da webcam
                e loop de predição (init → loop → predict)
```

Principais funções do script:

- `init()` — carrega o modelo (`model.json` + `metadata.json`) e inicia a webcam
- `loop()` — mantém a atualização contínua do frame da webcam
- `predict()` — roda a predição do modelo sobre o frame atual e atualiza os labels na tela

## Origem dos dados

Os dados usados para treinar o modelo **foram gravados por mim mesma**, diretamente pela webcam dentro do próprio Teachable Machine — não foi utilizado nenhum dataset externo ou pronto.

Foram capturadas amostras em vídeo/imagem para cada uma das três classes (`com carta`, `sem carta` e `fundo`), variando levemente ângulo, posição e enquadramento durante a gravação.

## Print do painel de treino

[Print do painel de treino](./prints/teachable-machine.png)

## Prints de uso do modelo

[Print com carta](./prints/print-com-carta.png)
[Print sem carta](./prints/print-sem-carta.png)
[Vídeo de demonstração](./prints/demonstração.mp4)

## Reflexão sobre os testes

Durante os testes, o modelo não confundiu as classes `com carta` e `sem carta`. Isso provavelmente aconteceu por causa do fundo sem informação, boa iluminação e falta de variação nos testes. Para conferir a qualidade real do modelo seria interessante testar em locais com fundo e iluminações diferentes.

## Como executar

1. Mantenha a pasta `my_model/` (com `model.json`, `metadata.json` e `weights.bin`) ao lado do `index.html` — não mova nem renomeie os arquivos.
2. Sirva a pasta com um servidor local (ex: extensão Live Server do VS Code) e abra pelo `http://localhost:...`. Alguns navegadores bloqueiam a webcam e o carregamento de arquivos locais quando o HTML é aberto direto do disco (`file:///...`).
3. Clique em **Iniciar câmera** e permita o acesso à webcam.
4. Posicione-se em frente à câmera, com ou sem a carta, e observe as probabilidades sendo atualizadas em tempo real.

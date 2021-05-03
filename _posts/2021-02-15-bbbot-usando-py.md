---
layout: post
title: "Robô de votação no paredão do BBB em Python"
comments: true
category: notes
description: "Praticando automação de interações na web na votação do BBB"
keywords: "bbb, bbb 2021, robo, automação, python, PyAutoGUI"
---

## BBBot
O intuito desse projeto é praticar interações autônomas com aplicações da web. Não esperamos _quebrar_ a página de votação do [_GShow_](https://gshow.globo.com/realities/bbb/), beneficiar ou prejudicar nenhum dos participantes.
Queremos automatizar a votação no BBB. No passado isso era possível usando Selenium (ferramenta para automação de funções do navegador), este ano o site do _GShow_ exige imediatamente um hcaptcha se detectar o Selenium. A solução então foi um robô que encontrasse imagens na tela e clicasse, em outras palavras, um simulador do clique humano, só que sem precisarmos operar o mouse. A solução não trabalha em segundo plano, mas ajuda entender o básico de captura e automação do mouse. Toda a aplicação pode ser encontrada no [repositório](https://github.com/m-rauh/bbbot-2021).

<meta property="og:image" itemprop="image" content="{{site.url}}/assets/post/brickgit.png" />

<div class="divider"></div>

## O projeto

Para essa aplicação utilizamos duas bibliotecas principais:
```
pip install pyautogui
pip install pillow
```

Como dito anteriormente, o programa trabalha em primeiro plano. Com a tela do site já aberta, a estrutura básica do programa é:
``` python
import pyautogui
from PIL import Image

brother = Image.open('img/brother.png')
captcha = Image.open('img/capt.png')
votar_nov =  Image.open('img/vot_again.png')

while True:
	
	#Localiza brother na tela, move o mouse até a posição e clica
	x, y = pyautogui.locateCenterOnScreen(brother)	
	pyautogui.moveTo(x, y)
	pyautogui.click()
	
	#Localiza "Sou Humano" na tela, move o mouse até a posição e clica
	w, z = pyautogui.locateCenterOnScreen(brother)	
	pyautogui.moveTo(w, z)
	pyautogui.click()
	
	#Localiza "Votar Novamente" na tela, move o mouse até a posição e clica
	a, b = pyautogui.locateCenterOnScreen(brother)	
	pyautogui.moveTo(a, b)
	pyautogui.click()
```

Se rodarmos o programa acima, ele sempre clicará no mesmo ponto da tela, que despertará o captcha do site. Queremos evitar isso. 
Além do mais, a aplicação tenta localizar a próxima imagem no instante seguinte que clica na tela. O site pode demorar um pouco para carregar, então nosso programa vai travar.
Para contornar essas questões, vamos trazer duas outras bibliotecas. Também vamos configurar o nosso movimento do mouse pra que ele fique mais aleatório e pareça mais humano.
```python
from random import randint
from random import uniform
from time import sleep

mouse_func = [pyautogui.easeOutBack, pyautogui.easeInOutQuad, pyautogui.easeOutQuad, pyautogui.easeInBounce, pyautogui.easeInElastic]
```

Assim podemos reescrever cada uma das interações:
```python
    sleep(100) # aguarda 1s pra tela aparecer
    
    x, y = pyautogui.locateCenterOnScreen(brother) # localiza imagem
    
    pyautogui.moveTo(w+randint(-20,150), z+randint(-30,10), uniform(0.5, 2.0), choice(mouse_func)) # move para uma posição aleatória entorno do x,y. O movimento dura de 0.5s à 2s e segue as configurações definidas
    
    pyautogui.click() #clica
```
Dessa forma nosso programa já consegue ser utlizado

<div class="divider"></div>

## Notas e Melhorias

O projeto, como dito anteriormente, é simples e criado para praticar o uso mais básico da automação da interface com usuário.
O potencial da aplicação é pequeno, se pensarmos em votos por minuto. Poderíamos fazer algumas melhorias pra que ele responda com mais velocidade, ainda assim, por ser um processo em primeiro plano com captura de tela, o potencial máximo do programa é limitado.
Obrigado, [Yuri](https://github.com/Rilufi), que deu o ponta pé pra criar essa aplicação e também já fez as outras implementações no BBB nos anos anteriores.

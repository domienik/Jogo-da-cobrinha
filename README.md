# Jogo-da-cobrinha
Famoso Snake Game com a biblioteca curses.

# `if __name__ == '__main__'`


Pelo o que entendi, está condicional irá verificar se o código que estamos executando está sendo executado no próprio script, caso contrario, se importamos esse script para outro código e tentássemos executar o trecho de código especifico com o `if __name__ == '__main__'` daria `False`. Isso se dá pelo momento que entregamos nosso código a outro script ele mudar sem nome atual de `__main__` para `__nome_do_script__`. Lembrando que `__name__` já tem seu valor predefinido como `__main__` no inicio de todo código.

```python
def game_loop():
    for i in range(10):
        print(f'O Valor de i é : {i}')
        time.sleep(1)

if __name__ == '__main__':
    game_loop()
```

# `time`

Iremos pausar o andamento do código para um segundo a cada iteração usando o método `time.sleep`.

# Biblioteca `curses`

O `curses` é um console novo mais interativo que ao ser executado, ele pausa a execução do nosso terminal principal.

Ela oferece uma variedade de funções para:

- **Manipular texto na tela:** Mover o cursor, imprimir caracteres, formatar texto com cores e atributos, gerenciar janelas e áreas de texto.
- **Tratar entrada do teclado:** Ler teclas pressionadas, detectar teclas especiais, gerenciar eventos de teclado e fornecer interatividade com o usuário.
- **Gerenciar a tela:** Controlar o tamanho e a forma da tela, atualizar a tela com eficiência, lidar com diferentes tipos de terminais e configurações.

`curses.wrapper` vai ser o método que criara um console interativo para todo o código que estiver dentro dos parênteses.

```python
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

Na nossa primeira função criada anteriormente, precisamos obrigatoriamente de uma ==janela== como argumento. No caso seria `window`.

```python
import time
import curses

def game_loop(window):
    for i in range(10):
        print(f'O Valor de i é : {i}')
        time.sleep(1)
        
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

Também, precisamos substituir o comando `print` da primeira função para `window.addstr`, uma vez que agora estamos trabalhando com um `console` diferente.

```python
import time
import curses

def game_loop(window):
    for i in range(10):
        window.addstr(f'O Valor de i é : {i}')
        time.sleep(1)
        
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

Por fim, nossa janela interativa ainda precisa de um ultimo detalhe, para a função imprimir de fato algo na tela a cada iteração precisamos usar o método `window.refresh()`, isso possibilita que o comando seja acionado na janela interativa.

```python
import time
import curses

def game_loop(window):
    for i in range(10):
        window.addstr(f'O Valor de i é : {i}')
        window.refresh()
        time.sleep(1)
        
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

No console apareceria algo assim:

<img width="637" height="31" alt="image" src="https://github.com/user-attachments/assets/91df4116-1c8f-4f13-9af0-001c7975229e" />

Outra coisa importante sobre o `curses` é que ele não possui uma quebra de linha automática. Isso quer dizer que suas "printagens" serão feitas uma após a outra. Para resolver isso é simples. Basta utilizarmos o:

```
\n
```

ficaria portanto:

```python
import time
import curses

def game_loop(window):
    for i in range(10):
        window.addstr(f'O Valor de i é : {i}\n')
        window.refresh()
        time.sleep(1)
        
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

# Interagindo com o terminal

Trocamos o loop for do nosso joguinho para um `while True`, isso significa que o código vai funcionar infinitamente a menos que definíssemos uma parada posteriormente nele.

```python
import time
import curses

def game_loop(window):
    while True:
        window.addstr(f'O Valor de i é : {i}\n')
        window.refresh()
        time.sleep(1)
        
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

Deletamos então o `window.addstr` para colocarmos uma nova variável no lugar.
- O nome da variável será `char` significa, caractere.
- adicionaremos dessa vez o método `window.getch()`
- `get` significa esperar a entrada de dados do usuário, no caso qualquer caractere do teclado.
- `ch` seria esse qualquer caractere.
- O código só ira ==prosseguir== se o usuário digitar qualquer coisa, caso contrario, o código ficara parado.

```python
import time
import curses

def game_loop(window):
    while True:
        char = window.getch()
        window.refresh()
        time.sleep(1)
        
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

Vamos também mostrar para o usuário a tecla que ele apertou. Usamos dessa vez `window.addstr` e digitamos algo para mostrar o tal caractere que foi adicionado anteriormente.
- Como não estamos mais utilizando o `time` podemos tirar ele por enquanto.
- Também podemos remover `window.refresh` pois a função `getch` está atualizando automaticamente para nós a cada instante.

```python
import curses

def game_loop(window):
    while True:
        char = window.getch()
        window.addstr(f'O caractere pressionado foi: {char}\n')
        
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

<img width="234" height="125" alt="image" src="https://github.com/user-attachments/assets/6615f782-db65-486c-be81-934f3288eba9" />

- É normal aparecer um erro caso não haja mais espaço no terminal.
- Como cada caractere do teclado é representado por um número, podemos descobrir posteriormente como localizar o número que representa as setas do teclado.
- O professor falou que `window.getch()` já atualiza sozinho o console e não precisamos da função auxiliar `refresh()` quando ele é acionado.

## timeout

A biblioteca curses também puxa uma função interessante do Python que é o `timeout`, essa função conta quanto tempo deve passar até o código continuar com a próxima função.

Podemos aplicar no nosso código para dar ao usuário um limite de tempo para digitar alguma tecla. Caso ele não digite, o programa vai retornar uma mensagem ou um sinalizar uma ação dizendo que a tecla não foi pressionada.

Isso será útil na hora de criar um joguinho, pois a cobrinha irá se locomover linearmente a cada segundo se o usuário não mudar a sua direção.

- `window.timeout` vai dar um tempo de 1 segundo para o usuário digitar algo. Caso contrario, o programa mostrará `'Nenhum caractere foi pressionado'`

```python
import curses

def game_loop(window):
    while True:
	    window.timeout(1000)
        char = window.getch()
        if char != -1:
	        window.addstr(f'O caractere pressionado foi: {char}\n')
	    else
		    window.addstr('Nenhum caractere foi pressionado\n')
        
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

# Evitar o erro de cair fora do terminal

Ao preencher todo o terminal da biblioteca `curses`, é comum ele retornar um erro dizendo que não há mais espaço para código. Porém, podemos evitar isso usando apenas um método simples:
- `window.clear`

Isso limpará a janela do terminal, abrindo espaço para mais códigos.

```python
import curses

def game_loop(window):
    while True:
        window.timeout(1000)
        char = window.getch()
        window.clear()
        if char != -1:
            window.addstr(f'O caractere pressionado foi: {char}\n')
        else:
            window.addstr('Nenhum caractere for pressionado.\n')
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

# Criando o personagem

- Tiramos então a quebra de linha pois como a janela está sendo limpa a cada segundo com o método `window.clear()`, não precisamos do `n`.

```python
import curses

def game_loop(window):
    while True:
        window.timeout(1000)
        char = window.getch()
        window.clear()
        if char != -1:
            window.addstr(f'O caractere pressionado foi: {char}')
        else:
            window.addstr('Nenhum caractere for pressionado.')
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

- Agora usaremos um tipo de condicional diferente para Python, para ficar mais legível e fácil de entender se a tecla alvo do teclado que queremos foi pressionada.
- O case que é apenas o `underline` vai retornar um `else`.

```python
import curses
def game_loop(window):
	window.addstr('Aperte alguma tecla')
    while True:
        window.timeout(1000)
        char = window.getch()
        window.clear()
        match char:
            case curses.KEY_UP:
                window.addstr('Mover para cima')
            case curses.KEY_LEFT:
                window.addstr('Mover para esquerda')
            case curses.KEY_DOWN:
                window.addstr('Mover para baixo')
            case curses.KEY_RIGHT:
                window.addstr('Mover para direita')
            case _:
                window.addstr('Tecla invalida')
                
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

Precisamos dar as coordenadas iniciais do nosso personagem.

- Podemos criar uma lista e defini-las no começo do código.

| Y                             | X                                     |
| ----------------------------- | ------------------------------------- |
| Coordenada de cima para baixo | Coordenada da esquerda para a direita |
- Substituímos então a parte `window.addstr('Aperte alguma tecla')` para os valores iniciais do personagem.
- O primeiro valor da lista será o nosso `y` (linha vertical) e o segundo o valor de `x` (linha horizontal).
- As coordenadas 10 e 15 serão as coordenadas padrão de onde o nosso personagem vai começar dentro do mapa.

```python
import curses
def game_loop(window):
	personagem = [10, 15]
    while True:
        window.timeout(1000)
        char = window.getch()
        window.clear()
        match char:
            case curses.KEY_UP:
                window.addstr('Mover para cima')
            case curses.KEY_LEFT:
                window.addstr('Mover para esquerda')
            case curses.KEY_DOWN:
                window.addstr('Mover para baixo')
            case curses.KEY_RIGHT:
                window.addstr('Mover para direita')
            case _:
                window.addstr('Tecla invalida')
                
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

- Usamos a função `addch` para representar o nosso personagem já que ele vai começar sendo apenas um caractere.
- Lembre-se que antes de definir apenas o caractere podemos definir os valores iniciais de sua localização inicial dentro do jogo.
- `curses.ACS_DIAMOND` vai ser um caractere especial em formato de diamante para representar nosso personagem
- A sintaxe ficaria assim:

| window.addch | coordenada y      | coordenada x      | Caractere Representativo |
| ------------ | ----------------- | ----------------- | ------------------------ |
| personagem   | 10                | 15                | `curses.ACS_DIAMOND`     |
|              | (`personagem[0]`) | (`personagem[1]`) |                          |


```python
import curses
def game_loop(window):
	personagem = [10, 15]
    window.addch(personagem[0], personagem[1], curses.ACS_DIAMOND)
    while True:
        window.timeout(1000)
        char = window.getch()
        window.clear()
        match char:
            case curses.KEY_UP:
                window.addstr('Mover para cima')
            case curses.KEY_LEFT:
                window.addstr('Mover para esquerda')
            case curses.KEY_DOWN:
                window.addstr('Mover para baixo')
            case curses.KEY_RIGHT:
                window.addstr('Mover para direita')
            case _:
                window.addstr('Tecla invalida')
                
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

Agora precisamos alterar as coordenadas do nosso personagem sempre que o usuário usar uma das setinhas do teclado.

- Alteramos os valores que imprimem algo na tela para adição e soma das coordenadas y e x.
- Devemos imprimir novamente a cobrinha sempre no final do loop pois ela está sendo removida no começo pelo método `window.clear`.
- Adicionamos `pass` na ultima condicional para o código não interpretar como erro caso o usuário digite algo errado ou diferente das 4 teclas mencionadas anteriormente ou caso ele simplesmente não faça nada no código depois de 1 segundo.

```python
import curses

def game_loop(window):
    personagem = [10, 15]
    window.addch(personagem[0], personagem[1], curses.ACS_DIAMOND)
    while True:
        window.timeout(1000)
        char = window.getch()
        window.clear()
        match char:
            case curses.KEY_UP:
                personagem[0] -= 1
            case curses.KEY_LEFT:
                personagem[1] -= 1
            case curses.KEY_DOWN:
                personagem[0] += 1
            case curses.KEY_RIGHT:
                personagem[1] += 1
            case _:
                pass
                
        window.addch(personagem[0], personagem[1], curses.ACS_DIAMOND)
if __name__ == '__main__':
    curses.wrapper(game_loop)
```

# Removendo o cursor

Simplesmente usamos o método `curses.curs_set(0)`, isso fará com que o cursor ou a barrinha horizontal que pisca desapareça.

- Com o método desativado:

  <img width="58" height="65" alt="image" src="https://github.com/user-attachments/assets/e86d8375-b0cb-4615-b3bc-bd7326030379" />

  - Com o método ativado
 
    <img width="43" height="43" alt="image" src="https://github.com/user-attachments/assets/60e404e8-1749-4341-8e23-53dab8285d28" />

# Adicionando uma borda nas margens

Usamos o método `window.border(0)`

Porem devemos repetir sempre após ter adicionado no começo para o método `window.clear()` não apagar a borda.

# Definindo condição de derrota

- Atribuímos as variáveis `height` e `width` para `window.getmaxyx()`, que retorna uma tupla contendo ambos valores e toda a largura representativa de colunas e linhas. Assim usamos desestruturação para atribuir os valores as variáveis.
- Fazemos uma condicional para verificar se o personagem está numa coordenada menor que o 0 (que basicamente é o quadrado inicial do código). e também fazemos para ver se ele está no limite máximo. que seria no caso `height` e `width`. Ambas coordenada são de `x` e `y`.
- Podemos enviar uma mensagem para o jogador avisando se ele perdeu ou ganhou o jogo. Caso perdesse, exibiria a mensagem `perdeu`, mas isso apenas se a função retornar algo.

```python
import curses

def game_loop(window):
    curses.curs_set(0)
    window.border(0)
    height, width = window.getmaxyx()
    personagem = [10, 15]
    window.addch(personagem[0], personagem[1], curses.ACS_DIAMOND)
    
    while True:
        window.timeout(1000)
        char = window.getch()
        window.clear()
        window.border(0)
        match char:
            case curses.KEY_UP:
                personagem[0] -= 1
            case curses.KEY_LEFT:
                personagem[1] -= 1
            case curses.KEY_DOWN:
                personagem[0] += 1
            case curses.KEY_RIGHT:
                personagem[1] += 1
            case _:
                pass
                
        if (personagem[0] <= 0) or (personagem[0] >= height-1):
            return
        if (personagem[1] <= 0) or (personagem[1] >= width-1):
            return
        window.addch(personagem[0], personagem[1], curses.ACS_DIAMOND)
        
if __name__ == '__main__':
    curses.wrapper(game_loop)
    print("Você perdeu")
```

<p style="font-size: 12px;">Repare que usamos -1 no width e no height, para o personagem não ficar literalmente na borda.</p>

# Organizando o código

- Agora iremos criar uma função especifica para cada parte do código para ficar mais legível.
- O código com todas as funções sendo chamadas ficaria assim:

```python
while True:
        draw_screen(window=window)
        draw_actor(actor=personagem, window=window)
        direction = get_new_direction(window=window, timeout=1000)
        if direction is not None:
            move_actor(actor=personagem, direction=direction)
        if actor_hit_border(actor=personagem, window=window):
            return
```

## Função desenhar a janela

- Passamos a função `window.clear` e `window.border(0)` para ela.
- Deletamos o `window.border` que estava no inicio do código.

```python
def draw_screen(window)
    window.clear()
    window.border(0)
```

## Função desenhar ator

- Passamos a função `window.addch(personagem[0], personagem[1], curses.ACS_DIAMOND)` para ela.
- Precisamos mudar dessa vez o `personagem` para `ator`, pois é o argumento que definimos para esta função.

```python
def draw_actor(actor, window)
    window.addch(actor[0], actor[1], curses.ACS_DIAMOND)
```

# Função obter nova direção

- Essa função basicamente irá armazenar a direção que o usuário escolheu e retornar essa direção. Caso o usuário não selecione nada num intervalo de tempo (`setTimeout`), o código retornará `Nule`
- Passamos então os métodos criados anteriormente que obtém a entrada de usuário e o método que define um limite de tempo (`getch` e `timeout`)

```python
def get_new_direction(window, timeout):
    window.timeout(1000)
    char = window.getch()
```

- Como iremos chamar essa função depois, podemos passar os milissegundos ao chamar ela, então não haverá problemas se definimos o valor dentro do parâmetro como uma variável.

```python
def get_new_direction(window, timeout):
    window.timeout(timeout)
    char = window.getch()
```

- Iremos mudar a variável `char` que armazena entrada de dados para `direction` dessa vez também.
```python
def get_new_direction(window, timeout):
    window.timeout(timeout)
    direction = window.getch()
```

- Agora para retornar um valor devemos fazer uma condicional. Caso a entrada de dados seja `KEY_UP KEY_DOWN KEY_LEFT KEY_RIGHT`, ele deve retornar o próprio valor da direção, caso contrario ele retorna `None`.

```python
def get_new_direction(window, timeout):
    window.timeout(1000)
    direction = window.getch()
    if direction in [curses.KEY_LEFT, curses.KEY_RIGHT,curses.KEY_DOWN, curses.KEY_UP]:
        return direction
    return None
```

# Função mover personagem

- Agora iremos mover o personagem se a função `get_new_direction` retornar um valor para nós.
- Ela receberá dois argumentos: `personagem` e `direction`. Ambas criadas anteriormente.

```python
def move_actor(actor, direction):
```

- Passamos toda nossas condicionais para verificar os botões pressionados pelo jogador para essa função e mudamos `char` para `direction`.
- Devemos trocar a variável antes nomeada de personagem para `actor` dessa vez também.
- Também não precisamos mas do `else` que retornava `pass` pois na função `get_new_direction` estamos garantindo que caso ele não corresponda os botões indicados ele retorne `None`.
- Sem as mudanças:

```python
match char:
	case curses.KEY_UP:
		personagem[0] -= 1
	case curses.KEY_LEFT:
		personagem[1] -= 1
	case curses.KEY_DOWN:
		personagem[0] += 1
	case curses.KEY_RIGHT:
		personagem[1] += 1
	case _:
		pass
```

- Com as mudanças:

```python
def move_actor(actor, direction):
    match direction:
        case curses.KEY_UP:
            actor[0] -= 1
        case curses.KEY_LEFT:
            actor[1] -= 1
        case curses.KEY_DOWN:
            actor[0] += 1
        case curses.KEY_RIGHT:
            actor[1] += 1
```

# Função para conferir se o `actor` bateu em uma das bordas.

- Passamos então a cadeia de condicionais que verifica se o personagem bateu na borda.
- Podemos passar a variável criada anteriormente que define os limites do jogo para essa função.
- Também não precisamos mais do método que desenha o personagem: ` window.addch(personagem[0], personagem[1], curses.ACS_DIAMOND)` pois já criamos uma função anteriormente para fazer isso para nós.
- Mudamos todos os personagens para `actor`.
- Também esperamos que a função retorne um booleano dessa vez. Então precisamos obrigatoriamente retornar um valor `True` caso o personagem caia para borda ou `False` caso aconteça nada.
- O loop infinito `While` vai ser parado caso a função `actor_hit_border` retorne `True`, sinalizando que o jogador perdeu: `if actor_hit_border(actor=personagem, window=window): return`
- Antes
```python
    if (personagem[0] <= 0) or (personagem[0] >= height-1):
        return
    if (personagem[1] <= 0) or (personagem[1] >= width-1):
        return
    window.addch(personagem[0], personagem[1], curses.ACS_DIAMOND)
```

- Depois

```python
def actor_hit_border(actor, window):
	height, width = window.getmaxyx()
    if (actor[0] <= 0) or (actor[0] >= height-1):
        return True
    if (actor[1] <= 0) or (actor[1] >= width-1):
        return True
    return False
```

# Mantendo o personagem em movimento

Precisamos certificar que a cobrinha se mecha automaticamente após um determinado tempo, modificaremos a chamada da função `get_new_direction` para invés de nos dar um valor `Nule`, obtemos o valor que o usuário já estava pressionando anteriormente.

- Podemos começar com uma variável para armazenar a direção que a cobra está indo, iremos começar com `KEY_DOWN` porem qualquer direção seria valida.
- O nome dessa variável será `current_direction`, iremos definir ela no inicio do código.

```python
def game_loop(window):
    curses.curs_set(0)
    personagem = [10, 15]
    current_direction = curses.KEY_DOWN
```

- Quando a direção for de fato `None` devemos mudar a variável `direction` para a `current_direction`, ou seja, quando o usuário não pressionar nada, ele vai andar na mesma direção anterior que ele estava.
- Agora a chamada da função `move_actor` pode ir para fora da endentação, visto que agora controlamos a direção do valor `Nule`. `move_actor` vai funcionar infinitamente a menos que o usuário bata na borda.
- No final do loop também devemos atualizar a `current_direction` para o valor contido na variável `direction`. Pois sempre que o usuário mover uma direção nova ou não, o `current_direction` deve armazenar ==obrigatoriamente== esse valor.
- Antes

```python
while True:
        draw_screen(window=window)
        draw_actor(actor=personagem, window=window)
        direction = get_new_direction(window=window, timeout=1000)
        if direction is not None:
            move_actor(actor=personagem, direction=direction)
        if actor_hit_border(actor=personagem, window=window):
            return
```

- Depois

```python
while True:
        draw_screen(window=window)
        draw_actor(actor=personagem, window=window)
        direction = get_new_direction(window=window, timeout=1000)
        if direction is None:
	        direction = current_direction
        move_actor(actor=personagem, direction=direction)
        if actor_hit_border(actor=personagem, window=window):
            return
        current_direction = direction
```

# Criando a cobra

Agora iremos criar definitivamente o corpo da cobrinha, onde cada parte única do seu corpo se mecha junto com ela.

- Começamos trocando o nome da variável `personagem` para `snake`.
- Também iremos criar uma lista de listas. onde uma lista armazena a localização da cobra no mapa (x e y), e a outra armazena uma coleção dessas localizações, sendo cada localização representando uma parte do corpo da cobra.
- Como a direção que a cobra começa no jogo é para baixo. Devemos ir diminuindo as coordenadas y conforme criamos o corpo da cobra para cada pedaço do ser corpo não cobrir o mesmo espaço.
- Antes

```python
def game_loop(window):
    curses.curs_set(0)
    personagem = [10, 15]
    current_direction = curses.KEY_DOWN
```

- Depois

```python
def game_loop(window):
    curses.curs_set(0)
    snake = [
        [10, 15], #Cabeça
        [9, 15],  #corpo 1
        [8, 15],  #corpo 2
        [7, 15]   #corpo 3
        ]
    current_direction = curses.KEY_DOWN
```

- Lógica da movimentação da cobrinha (exemplo fora do código)

```python
snake = [
    [5, 10]
    [6, 10] 
    [7, 10]
]

head = snake[0].copy() # Criará uma cópia da cabeça para evitar que modifiquemos o valor original
head[0] -= 1 # Diminuiremos a coordenada y da cópia (futuramente será escolhida a coordenada a ser diminuida)

snake.insert(0, head) # Inserimos a parte do corpo como primeiro item da lista

snake.pop() # Removemos o ultimo item da lista
```

<img width="1379" height="775" alt="image" src="https://github.com/user-attachments/assets/b5a66845-9565-4731-bf19-3c7c13ad0dc0" />

- Criamos uma nova função então no nosso código chamada `move snake`

```python
def move_snake(snake, direction):
    head = snake[0].copy()
    head[0] -= 1
    snake.insert(0, head)
    snake.pop()
```

- Como queremos que a cobra vá para a direção que o usuário mandar, devemos deletar a função `head[0] -= 1` e colocar nela a função `move_actor`, seus argumentos seriam a cabeça da cobrinha e a `direction`.

```python
def move_snake(snake, direction):
    head = snake[0].copy()
    move_actor(actor=head, direction=direction)
    snake.insert(0, head)
    snake.pop()
```

- Substituímos as funções criadas anteriormente de mover o ator para `move_snake`.
- Mudamos o argumento `actor=personagem` para `actor=snake`.
- Mudamos o nome da função `draw_actor` para `draw_snake` e novamente mudamos personagem para `snake` em seus argumentos.
- Mudamos `actor_hit_border` para `snake_hit_border`
- Mudamos `move_actor` para `move_snake`
- Antes

```python
    while True:
        draw_screen(window=window)
        draw_snake(actor=personagem, window=window) 
        direction = get_new_direction(window=window, timeout=300)
        if direction is None:
            direction = current_direction
        move_actor(actor=personagem, direction=direction) 
        if actor_hit_border(actor=personagem, window=window):
            return
        current_direction = direction
```

- Depois

```python
    while True:
        draw_screen(window=window)
        draw_snake(snake=snake, window=window)
        direction = get_new_direction(window=window, timeout=300)
        if direction is None:
            direction = current_direction
        move_snake(snake=snake, direction=direction)
        if snake_hit_border(snake=snake, window=window):
            return
        current_direction = direction
```

# Desenhar a cobra

## Cabeça

- Agora iremos criar uma nova função chamada `draw_snake`, ela receberá com argumentos `snake` e `window`.

```python
def draw_snake(snake, window):
```

- Vamos modelar agora a função `draw_actor` para receber um novo argumento `char`, agora iremos poder escolher qual símbolo poderá representar a cabeça da cobrinha.

```python
def draw_actor(actor, window, char):
    window.addch(actor[0], actor[1], char)
```

- Dentro da função `draw_snake` obtemos a lista que representa a cabeça da cobra e depois passamos como argumento para a função `draw_actor`
- A cabeça da cobra será representada como `@`
- Lembrando que precisamos do argumento `window.addch` para escolher o lugar que a cobra se localizará no mapa. O primeiro argumento é a localização y, o segundo a localização x e por fim o caractere que representativo.

```python
def draw_snake(snake, window):
    head = snake[0]
    draw_actor(actor=head, window=window, char="@")
```

## Corpo

Para desenhar o resto do corpo não mudaremos nada na função `draw_actor`, mas precisaremos percorrer todos os elementos que estão dentro da lista `snake`, lembrando que cada elemento é uma lista com duas coordenadas.
- Definimos uma variável chamada `body` e atribuimos a ela uma lista contendo todas as partes da cobra menos a cabeça usando `[1::]`
- Para cada parte da cobra iremos acionar a função `draw_actor`, fazemos isso com o auxilio de uma laço `for`. Cada elemento da cobra se localizará atrás da cabeça com o caractere `s`.

```python
def draw_snake(snake, window):
    head = snake[0]
    draw_actor(actor=head, window=window, char="@")
    body = snake[1::]
    for body_part in body:
        draw_actor(actor=body_part, window=window, char="s")
```

# Checar se a cabeça bateu na borda

Praticamente já temos uma função que checa se o personagem bateu na borda. Porém dessa vez queremos que a cabeça em especifico seja verificada se bateu na borda e não o resto do corpo.

- Perceba que chamamos a função `actor_hit_border`, sendo o ator a cabeça.

```python
def snake_hit_border(snake, window):
    head = snake[0]
    return actor_hit_border(actor=head, window=window) # Estamos checando se só a cabeça bateu na borda.
```

# Criar frutinhas

 Iremos criar frutinhas que aumentam o tamanho da cobra conforme pegamos cada uma. Elas serão distribuídas em ordem aleatória pelo mapa.

- Começamos com a função `import random` no começo do nosso código.
- Criamos uma variável chamada `fruit` onde receberá uma chamada de função como valor, e essa função terá como argumento `window` para obtermos a dimensão de onde as frutas poderão ser geradas no mapa.
- Colocamos então essa variável no começo do código e também um dentro do loop sempre que a cobrinha for pegar uma nova fruta.
- Preste atenção nos ==hashtags==

```python
import curses #Mudança 1
import random

def game_loop(window):
    curses.curs_set(0)
    snake = [
        [10, 15],
        [9, 15],
        [8, 15],
        [7, 15]
        ]
        
    fruit = get_new_fruit(window=window) # Mudança 2
    current_direction = curses.KEY_DOWN
    
    while True:
        draw_screen(window=window)
        draw_snake(snake=snake, window=window)
        direction = get_new_direction(window=window, timeout=300)
        if direction is None:
            direction = current_direction
        move_snake(snake=snake, direction=direction)
        if snake_hit_border(snake=snake, window=window):
            return
            
        if snake_hit_fruit(snake=snake, fruit=fruit): # Mudança 3
            fruit = get_new_fruit(window=window)
            
        current_direction = direction
```

- Também iremos reaproveitar a função `draw_actor` para desenhar as frutas.
- Colocamos ela no inicio do nosso loop.

```python
import curses 
import random

def game_loop(window):
    curses.curs_set(0)
    snake = [
        [10, 15],
        [9, 15],
        [8, 15],
        [7, 15]
        ]
        
    fruit = get_new_fruit(window=window) 
    current_direction = curses.KEY_DOWN
    
    while True:
        draw_screen(window=window)
        draw_snake(snake=snake, window=window)
        draw_actor(actor=fruit, window=window, char=curses.ACS_DIAMOND) #mudança 1
        direction = get_new_direction(window=window, timeout=300)
        if direction is None:
            direction = current_direction
        move_snake(snake=snake, direction=direction)
        if snake_hit_border(snake=snake, window=window):
            return
            
        if snake_hit_fruit(snake=snake, fruit=fruit): 
            fruit = get_new_fruit(window=window)
            
        current_direction = direction
        ```

# Função criar nova fruta

- Começamos então a passar o método `window.getmaxyx()` dentro da função para obter os limites do mapa.

```python
def get_new_fruit(window):
    height, width = window.getmaxyx()
```

Iremos retornar uma lista que contem duas coordenadas, ambas serão aleatórias.

```python
return [random.randint(1, height-2), random.randint(1, width-2)]
```

A função na sua totalidade ficaria assim:

- Criamos `get_new_fruit e passamos` a `window` como argumento.
- Iremos obter a largura e altura da janela novamente usando `window.getmaxyx()`
- Retornamos então uma localização aleatória no mapa usando o `random.randint`

```python
def get_new_fruit(window):
    height, width = window.getmaxyx()
    return [random.randint(1, height-2), random.randint(1, width-2)]
```

# Função cobrinha bateu na fruta

Será uma função bem simples que retornará `True` ou `False`, ela checará se a cobrinha bateu na fruta, isso será possível se checarmos se as coordenadas da fruta estão dentro da lista de coordenadas da cobrinha.

```python
def snake_hit_fruit(snake, fruit):
    return fruit in snake
```

# Aumentar a cobrinha de tamanho

Iremos alterar uma das funções criadas anteriormente:
- Como sabemos, `snake.pop` é usado apenas para locomover a cobra, como se ela estivesse se deletando se criando mutualmente, porem se pularmos o `pop()` conseguiremos aumentar ela de tamanho apenas.

```python
def move_snake(snake, direction):
    head = snake[0].copy()
    move_actor(actor=head, direction=direction)
    snake.insert(0, head)
    snake.pop()
```

- Então queremos fazer uma condição para essa função, passamos um novo argumento para ela `snake_ate_fruit` onde obteremos um `booleano`
- Sempre que `snake_ate_fruit` for `false` ele ativará o `snake.pop()`, fazendo que com a cobra continue no seu tamanho normal
- Caso contrario ela aumentará sem tamanho.

```python
def move_snake(snake, direction, snake_ate_fruit):
    head = snake[0].copy()
    move_actor(actor=head, direction=direction)
    snake.insert(0, head)
    if not snake_ate_fruit:
        snake.pop()
```

- Voltamos para o começo do código e passamos como argumento para a chamada de função `move_snake` a variavel `snake_ate_fruit`
- `snake_ate_fruit` vai ser definida como `False` nos primeiros parâmetros do jogo e depois dentro da função `snake_hit_fruit` mudamos para `True`.
- Para o `True` não ficar infinitamente mudamos ele para `False` caso o jogador não bata na fruta.
- Repare os ==hashtags.==

```python
import curses
import random

def game_loop(window):
    curses.curs_set(0)
    snake = [

        [10, 15],

        [9, 15],

        [8, 15],

        [7, 15]

        ]

    fruit = get_new_fruit(window=window)

    current_direction = curses.KEY_DOWN

    snake_ate_fruit = False                            # M

  

    while True:

        draw_screen(window=window)

        draw_snake(snake=snake, window=window)

        draw_actor(actor=fruit, window=window, char=curses.ACS_DIAMOND)

        direction = get_new_direction(window=window, timeout=300)

        if direction is None:

            direction = current_direction

        move_snake(snake=snake, direction=direction, snake_ate_fruit=snake_ate_fruit) #M

        if snake_hit_border(snake=snake, window=window):

            return

        if snake_hit_fruit(snake=snake, fruit=fruit):

            snake_ate_fruit = True                          #M

            fruit = get_new_fruit(window=window)

        else:                                           #M
            snake_ate_fruit = False
            
        current_direction = direction
```

# Checando colisão

Agora iremos checar se a cobra bateu nela mesma. Começamos chamando a função e passamos como argumento a própria cobra. Se ela bateu, o jogo finalizará.

```python
if snake_hit_itself(snake=snake):
            return
```

- Criamos então a função. Criamos duas variáveis, uma para armazenar a cabeça da cobra e outra para armazenar o resto do corpo. Se a cabeça estiver dentro do corpo da cobra, a função retorna `True` levando o usuário a derrota.

```python
def snake_hit_itself(snake):
    head = snake[0]
    body = snake[1:]
    return head in body
```

# Checando colisão oposta

Agora checaremos se o usuário apertar a direção oposta que ele está indo, caso correto, a cobrinha ira cancelar esse comando.

- Se o usuário então foi em uma direção oposta que ele estava indo, a função neutralizar essa direção e manter a mesma.
```python
if direction_is_opposite(direction=direction, current_direction=current_direction):
            direction = current_direction
```

- Aqui observamos que caso o usuário aperte a tecla `KEY_UP`, ele vai checar se a direção anterior que a cobrinha estava era sua direção inversa, caso fosse, ela retornaria `True`.
- `current_direction` só irá mudar no final do loop do jogo. Então não precisamos nos preocupar com sua interferência direta nessa parte do código.

```python
def direction_is_opposite(direction, current_direction):
    match direction:
        case curses.KEY_UP:
            return current_direction == curses.KEY_DOWN
        case curses.KEY_LEFT:
            return current_direction == curses.KEY_RIGHT
        case curses.KEY_DOWN:
            return current_direction == curses.KEY_UP
        case curses.KEY_RIGHT:
            return current_direction == curses.KEY_LEFT
```

# Finalizando o jogo

Criamos uma variável `score` nos primeiros parâmetros do jogo, e ela irá aumentar sempre que o usuário `hitar` na fruta.

```python
score = 0

if snake_hit_fruit(snake=snake, fruit=fruit):
            snake_ate_fruit = True
            fruit = get_new_fruit(window=window)

            score += 1
```

Criamos uma função também que finaliza o jogo, ela irá mostrar quantas frutas o usuário pegou e a mensagem de "Você perdeu" no meio da tela.
- Mudamos todos os `returns` do jogo para `break`
- a variável y vai armazenar o valor máximo do y (linha vertical) dividido por 2, é importante que seja `int()` para não dar um número quebrado.
- A variável `x` vai armazenar metade do valor total da linha horizontal. Subtraímos também todos os caracteres da mensagem que iremos mostrar ao usuário para caber direitinho no meio, caso contrário, ela sairia mais para a direita (`x = int((width - len(s))/2)`).
- Imprimimos na tela final `window.addstr(y, x, s)`
- Adicionamos agora também `window.refresh` para atualizar o terminal e `time.sleep` para o jogo retornar o `cmd` normal depois de 2 segundos.

```python
    finish_game(score, window)

def finish_game(score, window):
    height, width = window.getmaxyx()
    s = f'Você perder! Coletou{score} frutas'
    y = int(height/2)
    x = int((width - len(s))/2)
    window.addstr(y, x, s)
    window.refresh()
    time.sleep(2)
```

- Podemos remover agora no final do jogo a mensagem de `você perdeu`.

```python
if __name__ == '__main__':
    game_loop()
    print("você perdeu")
```

```python
if __name__ == '__main__':
    game_loop()
```

# Selecionar dificuldade do jogo

Adicionamos uma nova variável no argumento primordial do nosso jogo chamada `game_speed`, podemos colocar ela no lugar do número do `timeout`.

- Antes

```python
direction = get_new_direction(window=window, timeout=300)
```

- Depois

```python
direction = get_new_direction(window=window, timeout=game_speed)
```

- Adicionamos `game_speed` no começo do jogo.

```python
def game_loop(window, game_speed):
    curses.curs_set(0)
    snake = [
        [10, 15], # Restante do código...
```

- Devemos colocar no final do código também.

```python
if __name__ == '__main__':

    curses.wrapper(game_loop, game_speed)
```

- Agora passamos uma função para definir a dificuldade do jogo como argumento para `game_speed`

```python
if __name__ == '__main__':
    curses.wrapper(game_loop, game_speed=select_difficult())
```

- Na função, criamos um dicionário com várias chaves contendo números e suas respectivas dificuldades, e enquanto o usuário não selecionar uma dificuldade correta o `loop while True` vai continuar infinitamente.

```python
def select_difficult():

    difficulty = {
        '1' : 1000,
        '2' : 500,
        '3' : 150,
        '4' : 90,
        '5' : 35,
    }

    while True:
        answer = input("Selecione a dificuldade de 1 a 5")
        game_speed = difficulty.get(answer)
        if game_speed is not None:
            return game_speed
```







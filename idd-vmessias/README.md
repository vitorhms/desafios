```
 _____                _                  _         _____                _  _           _ 
|  __ \              | |                | |       /  __ \              (_)| |         | |
| |  \/  __ _  _ __  | |__    ___     __| |  ___  | /  \/  __ _  _ __   _ | |_   __ _ | |
| | __  / _` || '_ \ | '_ \  / _ \   / _` | / _ \ | |     / _` || '_ \ | || __| / _` || |
| |_\ \| (_| || | | || | | || (_) | | (_| ||  __/ | \__/\| (_| || |_) || || |_ | (_| || |
 \____/ \__,_||_| |_||_| |_| \___/   \__,_| \___|  \____/ \__,_|| .__/ |_| \__| \__,_||_|
                                                                | |                      
                                                                |_|                      
ganho-de-capital 0.1.0
```
<p align="center">
  <i>Code Challenge: Ganho de Capital</i> programa de linha de comando (CLI) que calcula o imposto a ser pago sobre lucros ou prejuízos de operações no mercado financeiro de ações.
</p>

<p align="center">
  <a href="#descrição">Descrição</a> •
  <a href="#regras">Regras</a> •
  <a href="#exemplo">Exemplo</a> •
  <a href="#arquitetura">Arquitetura</a> •
  <a href="#bibliotecas">Bibliotecas</a> •
  <a href="#instalação">Instalação</a> •
  <a href="#executando">Executando</a> •
  <a href="#considerações">Considerações</a>
</p>

---

# Descrição

O programa recebe listas, uma por linha, de operações do mercado financeiro de ações em formato
json através da entrada padrão ( stdin ). Cada operação desta lista contém os seguintes campos:
```
┏━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃    Nome    ┃ Significado                                                      ┃
┡━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
┃ operation  ┃ Se a operação é uma operação de compra ( buy ) ou venda ( sell ) ┃
┃ unit-cost  ┃ Preço unitário da ação em uma moeda com duas casas decimais      ┃
┃ quantity   ┃ Quantidade de ações negociadas                                   ┃
┡━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
```
Este é um exemplo da entrada:
```JSON
[{"operation":"buy", "unit-cost":10.00, "quantity": 10000}, {"operation":"sell", "unit-cost":20.00, "quantity": 5000}]
[{"operation":"buy", "unit-cost":20.00, "quantity": 10000}, {"operation":"sell", "unit-cost":10.00, "quantity": 5000}]

```

As operações estarão na ordem em que elas ocorreram, ou seja, a segunda operação na lista aconteceu
depois da primeira e assim por diante.
Cada linha é uma simulação independente, seu programa não deve manter o estado obtido em uma linha
para as outras.
A última linha da entrada será uma linha vazia.

Para cada linha da entrada, o programa deve retornar uma lista contendo o imposto pago para cada
operação recebida. Os elementos desta lista devem estar codificados em formato json e a saída deve ser
retornada através da saída padrão ( stdout ). O retorno é composto pelo seguinte campo:
```
┏━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃    Nome    ┃ Significado                              ┃
┡━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
┃ tax        ┃ O valor do imposto pago em uma operação  ┃
┡━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
```
Este é um exemplo da saída:
```JSON
[{"tax":0.00}, {"tax":10000.00}]
[{"tax":0.00}, {"tax":0.00}]
```

A lista retornada pelo programa deve ter o mesmo tamanho da lista de operações processadas na entrada.
Por exemplo, se foram processadas três operações (buy, buy, sell), o retorno do programa deve ser uma lista
com três valores que representam o imposto pago em cada operação.

# Regras

O programa deve lidar com dois tipos de operações ( buy e sell ) e ele deve seguir as seguintes regras:
- O percentual de imposto pago é de 20% sobre o lucro obtido na operação. Ou seja, o imposto vai ser pago quando há uma operação de venda cujo preço é superior ao preço médio ponderado de compra.
- Para determinar se a operação resultou em lucro ou prejuízo, você pode calcular o preço médio ponderado, então quando você compra ações você deve recalcular o preço médio ponderado utilizando essa fórmula: nova-media-ponderada = ((quantidade-de-acoes-atual * media-ponderada-atual) + (quantidade-de-acoes * valor-de-compra)) / (quantidade-de-acoes-atual + quantidade-de-acoes-compradas) . Por exemplo, se você comprou 10 ações por R$ 20,00, vendeu 5, depois comprou outras 5 por R$ 10,00, a média ponderada é ((5 x 20.00) + (5 x 10.00)) / (5 + 5) = 15.00 .
- Você deve usar o prejuízo passado para deduzir múltiplos lucros futuros, até que todo prejuízo seja deduzido.
- Prejuízos acontecem quando você vende ações a um valor menor do que o preço médio ponderado de compra. Neste caso, nenhum imposto deve ser pago e você deve subtrair o prejuízo dos lucros seguintes, antes de calcular o imposto.
- Você não paga nenhum imposto se o valor total da operação (custo unitário da ação x quantidade) for menor ou igual a R$ 20000,00. Use o valor total da operação e não o lucro obtido para determinar se o imposto deve ou não ser pago. E não se esqueça de deduzir o prejuízo dos lucros seguintes.
- Nenhum imposto é pago em operações de compra.

Você pode assumir que nenhuma operação vai vender mais ações do que você tem naquele momento.

# Exemplo

Quando a aplicação recebe duas linhas, elas devem ser lidadas como duas simulações independentes. O
programa não deve carregar o estado obtido do processamento da primeira entrada para as outras
execuções.

Input:
```JSON
[{"operation":"buy", "unit-cost":10.00, "quantity": 100}, {"operation":"sell", "unit-cost":15.00, "quantity": 50}, {"operation":"sell", "unit-cost":15.00, "quantity": 50}]
[{"operation":"buy", "unit-cost":10.00, "quantity": 10000}, {"operation":"sell", "unit-cost":20.00, "quantity": 5000}, {"operation":"sell", "unit-cost":5.00, "quantity": 5000}]

```
Output:
```JSON
[{"tax": 0.00}, {"tax": 0.00},{"tax": 0.00}]
[{"tax": 0.00}, {"tax": 10000.00},{"tax": 0.00}]
```

# Arquitetura

- Kotlin v1.8
- Gradle v7.4.2
- Docker

# Bibliotecas

- Mockk 1.13.4
- Jackson 2.14.2
- Kotlin Json Serialization 1.4.1

# Instalação

* Necessário Docker instalado.

Gerar imagem da aplicação:
```SHELL
docker build -t ganho-capital .
```

# Executando

- Executando no terminal:
```SHELL
docker run -it --rm ganho-capital
```
A aplicação vai aguardar o input das informações e vai devolver o resultado quando receber uma linha vazia.

- Executando via Input Redirection:
```SHELL
docker run -i --rm ganho-capital < src/test/resources/input.txt
```
Ou
```SHELL
docker run -i --rm ganho-capital << EOF
[{"operation":"buy", "unit-cost":10.00, "quantity": 100}, {"operation":"sell", "unit-cost":15.00, "quantity": 50}, {"operation":"sell", "unit-cost":15.00, "quantity": 50}]
[{"operation":"buy", "unit-cost":10.00, "quantity": 10000}, {"operation":"sell", "unit-cost":20.00, "quantity": 5000}, {"operation":"sell", "unit-cost":5.00, "quantity": 5000}]

EOF
```

- Executando com debug:
```SHELL
docker run -eDEBUG=true [....]
```

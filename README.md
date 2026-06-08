# Dispositivo de Papel com Grafite para Classificação de Texturas a partir de Redes Neurais

## Visão Geral
Este projeto investiga o uso de um **dispositivo físico construído em papel com grafite condutivo** como sensor resistivo para captura de sinais elétricos associados a diferentes texturas. Os sinais obtidos são processados computacionalmente e utilizados para treinar modelos de Machine Learning, com foco em classificação de padrões físicos.

---
## Introdução e objetivos

## Fabricação do dispositivo
O dispositivo foi fabricado sobre um substrato de papel filtro 80 g/m² com dimensões de 6 cm × 3 cm. A região sensora foi obtida por deposição manual de grafite utilizando lápis 6B, devido à sua elevada condutividade elétrica e boa aderência ao papel. A deposição repetida promove a formação de uma rede condutiva contínua, aumentando os caminhos para o transporte eletrônico (KIM _et al_, 2021).
O processo de fabricação seguiu as seguintes etapas:

1.	Recorte do substrato de papel filtro nas dimensões especificadas.
2.	Deposição de grafite com lápis 6B em uma área retangular de 1,5 cm × 3 cm, formando o elemento sensor resistivo.
3.	Introdução controlada de microfissuras (cracks) na região grafitada por meio de deformação mecânica local.
4.	Trilhos condutores de tinta de prata nas extremidades da região grafitada, atuando como eletrodos de contato elétrico para conexão ao analisador de parâmetros Keithley.
5.	Delimitação da área ativa do sensor utilizando fita adesiva, deixando exposta apenas uma região circular central com diâmetro de 0,8 cm.
6.	Conexão dos eletrodos ao sistema de aquisição para realização das medições elétricas.
   
A introdução de microfissuras foi inspirada em sensores piezoresistivos baseados em trincas descritos por Kang et al. (2014). Essas descontinuidades atuam como regiões críticas para o transporte de cargas, de modo que pequenas deformações mecânicas alteram a conectividade entre os caminhos condutivos e produzem variações significativas na resistência elétrica, aumentando a sensibilidade do dispositivo (KANG _et al_., 2014).

A operação do dispositivo baseia-se na variação da resistência elétrica da camada de grafite em resposta a estímulos mecânicos aplicados sobre a área ativa.

### Especificações do dispositivo

| Parâmetro              | Valor                                     |
| ---------------------- | ----------------------------------------- |
| Substrato              | Papel filtro 80 g/m²                      |
| Dimensões do substrato | 6 cm × 3 cm                               |
| Material sensível      | Grafite depositado com lápis 6B           |
| Área grafitada         | 1,5 cm × 3 cm                             |
| Área ativa             | Circular                                  |
| Diâmetro da área ativa | 0,8 cm                                    |
| Eletrodos              | Tinta condutiva de prata                  |
| Número de eletrodos    | 2                                         |
| Equipamento de medição | Keithley SourceMeter / Parameter Analyzer |



## Medidas
As análises exploraram três texturas: PARAFILM®, papel texturizado e lixa, com quatro dispositivos dedicados a cada uma. As medições elétricas foram conduzidas no Analisador de Parâmetros Keithley SourceMeter, com auxílio de uma balança para ter noção da força/pressão aplicada na amostra. Para cada dispostivo, seguiu-se os seguintes passos:

1. Medição de corrente em função da diferença de potencial aplicada, para um intervalo de 0 a 1 V, aumentando e depois diminuindo os valores;
2. Extração dos valores de corrente em 0.5 V (ida e volta) para utilizar sua média como referência (I_ref);
3. Medição dos valores de corrente pelo tempo, aplicando 0.5 V de tensão, durante 1 min. Começando sem pressionar, a cada 10 s, realizou-se uma pressão que aumentasse em 100 g a massa indicada na balança, obtendo valores em uma faixa de 0 a 500 g.

O passo 3 foi gravado em celular para registrar os valores de massa em função do tempo. Porém, como o processo era totalmente dependente do operador, houveram muitas imprecisões atreladas a variação da pressão. 

Os dados foram armazenados localmente no formato `.csv` e repassados às alunas por repositório interno.

## Tratamento de dados

## Construção das redes

### MLP simples

### SNN com arquitetura MLP

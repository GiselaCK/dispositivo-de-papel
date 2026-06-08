# Dispositivo de Papel com Grafite para Classificação de Texturas a partir de Redes Neurais

## Visão Geral
Este projeto investiga o uso de um **dispositivo físico construído em papel com grafite condutivo** como sensor resistivo para captura de sinais elétricos associados a diferentes texturas. Os sinais obtidos são processados computacionalmente e utilizados para treinar modelos de Machine Learning, com foco em classificação de padrões físicos.

---
## Introdução e objetivos

## Fabricação do dispositivo
O dispositivo foi fabricado sobre um substrato de papel filtro 80 g/m² com dimensões de 6 cm × 3 cm. A região sensora foi obtida por deposição manual de grafite utilizando um lápis 6B, escolhido por apresentar elevada condutividade elétrica e boa aderência ao substrato de papel. Esse método de deposição repetida de grafite promove a formação de uma rede condutiva contínua, reduzindo a rugosidade superficial do papel e aumentando os caminhos disponíveis para o transporte eletrônico (KIM, 2021).
O processo de fabricação seguiu as seguintes etapas:

1.	Recorte do substrato de papel filtro nas dimensões especificadas.
2.	Deposição de grafite com lápis 6B em uma área retangular de 1,5 cm × 3 cm, formando o elemento sensor resistivo.
3.	Introdução controlada de microfissuras (cracks) na região grafitada por meio de deformação mecânica local. Essa etapa foi inspirada em sensores biológicos e em dispositivos piezoresistivos baseados em trincas descritos por Kang et al. (2014), nos quais pequenas descontinuidades na camada condutiva aumentam significativamente a sensibilidade elétrica do sensor.
4.	Aplicação de dois trilhos condutores de tinta de prata nas extremidades da região grafitada, atuando como eletrodos de contato elétrico para conexão ao analisador de parâmetros Keithley.
5.	Delimitação da área ativa do sensor utilizando fita adesiva, deixando exposta apenas uma região circular central com diâmetro de 0,8 cm.
6.	Conexão dos eletrodos ao sistema de aquisição para realização das medições elétricas.
   
A introdução de microfissuras na camada de grafite tem como objetivo aumentar a resistividade inicial do dispositivo e amplificar sua resposta a estímulos mecânicos. De maneira análoga ao mecanismo observado no sistema sensorial das aranhas e reproduzido por Kang et al. (2014), as trincas atuam como regiões críticas para o transporte de cargas elétricas. Quando uma força é aplicada sobre o sensor, ocorrem variações na abertura e no fechamento dessas microfissuras, modificando a conectividade entre os caminhos condutivos de grafite. Como consequência, pequenas deformações mecânicas produzem variações expressivas na resistência elétrica, aumentando a sensibilidade do dispositivo (KANG et al, 2014).
A operação do dispositivo baseia-se, portanto, na variação da resistência elétrica da camada de grafite em resposta a estímulos mecânicos aplicados sobre a área ativa. A fita adesiva atua simultaneamente como máscara geométrica e elemento de confinamento da região sensora, garantindo que as medidas sejam realizadas sempre sobre uma área efetiva constante. Os eletrodos de tinta de prata reduzem a resistência de contato entre o material sensível e o equipamento de medição, permitindo a aquisição reprodutível dos parâmetros elétricos.

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

## Tratamento de dados

## Construção das redes

### MLP simples

### SNN com arquitetura MLP

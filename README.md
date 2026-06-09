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


O tratamento dos dados foi realizado em três etapas sequenciais implementadas em notebooks Python: cálculo da variação relativa de corrente, adição dos valores de massa por OCR e rotulação das amostras.

### 1. Extração da Variação Relativa de Corrente (`tocsv`)

Cada amostra possui arquivos `.xls` contendo curvas corrente × tensão (I-V) e corrente × tempo (I-t).

A corrente de referência `I₀` foi definida como a média dos valores de corrente medidos em `V = 0,500 V` durante as varreduras de subida e descida da curva I-V, reduzindo efeitos de histerese. A partir desse valor, foi calculada a variação relativa de corrente para cada instante da curva I-t:

```math
\frac{\Delta I}{I_0}=\frac{I(t)-I_0}{I_0}
```
A variação relativa de corrente foi usada devido a diferença de resistividade entre os diferentes dispositivos fabricados, a fim de evitar que a resistividade intrínseca do dispositivo impactasse na classificação de textura. 

Os resultados foram armazenados em arquivos `VariacaoRelativa_Amostra{N}.csv`, contendo as colunas:

- `tempo`
- `corrente`
- `tensao`
- `variacao_relativa`

### 2. Adição dos Valores de Massa (`adicionamassa`)

A massa aplicada aos dispositivos foi registrada por uma balança cujo visor foi filmado em vídeos `video_{N}.mp4`. Como não havia saída digital da balança, foi desenvolvido um pipeline de visão computacional para extrair automaticamente os valores de massa.

Para cada instante de tempo presente nos CSVs:

1. O frame correspondente foi localizado utilizando o FPS do vídeo.
2. A região do visor da balança foi recortada por uma ROI previamente definida.
3. O valor numérico foi extraído utilizando EasyOCR.

Foram aplicadas diferentes estratégias de pré-processamento até a obtenção de uma leitura válida, incluindo:

- binarização fixa;
- binarização por Otsu;
- segmentação HSV dos dígitos vermelhos;
- dilatação morfológica;
- CLAHE seguido de binarização.

O OCR foi executado com caracteres restritos a números e ponto decimal.

Como o protocolo experimental utilizava degraus discretos de massa (0, 100, 200, 300, 400 e 500 g), leituras com erro superior a 80 g foram corrigidas utilizando a massa esperada para a respectiva janela temporal ou o valor válido mais próximo.

Além da massa final, foram armazenados campos de auditoria:

- `ocr_bruto`
- `estrategia`
- `correcao`

Os resultados foram salvos em `Completo_Amostra{N}.csv`, combinando os valores de variação relativa e massa.

Vale ressaltar que, devido às limitações da montagem experimental, a leitura dos valores da balança apresentou baixa acurácia, sendo necessária a substituição de diversas medições pelo valor esperado com base no protocolo experimental, a fim de reduzir erros de aquisição.

### 3. Rotulação das Amostras (`rotulando`)

Os arquivos completos foram rotulados de acordo com a superfície do dispositivo:

| Amostras | Rótulo | Classe |
|-----------|---------|---------|
| 1–4 | 0 | Papel filme |
| 5–8 | 1 | Papel texturizado |
| 9–12 | 2 | Lixa |

A coluna `rotulo` foi adicionada a cada arquivo CSV.

### Dataset Final

Após o processamento, cada registro contém as colunas `variacao_relativa`, `massa_g` e `rotulo`, entre as outras citadas anteriormente nesta seção. As três colunas referenciadas foram utilizadas para a geração das janelas temporais e treinamento da rede neural.


## Construção das redes

### MLP simples

### SNN com arquitetura MLP
  
#### Arquitetura da SNN 
  
A rede neural de pulsos (SNN) implementada segue uma topologia totalmente conectada com três blocos sequenciais, cada um composto por uma camada linear seguida de um neurônio *Leaky Integrate-and-Fire* (LIF): 
  
``` 
Entrada (2) → Linear(2→32) → LIF → Linear(32→16) → LIF → Linear(16→3) → LIF 
``` 
  
Os dois atributos de entrada são a variação relativa de corrente (`variacao_relativa`) e a massa depositada sobre o dispositivo (`massa_g`). A saída possui três neurônios, correspondentes às classes papel filme (0), papel texturizado (1) e lixa (2). 
  
O neurônio LIF modela de forma simplificada a dinâmica de um neurônio biológico: o potencial de membrana integra a corrente recebida e decai exponencialmente entre passos temporais com fator `β = 0,9`. Quando o potencial supera o limiar, o neurônio emite um pulso binário (*spike*) e seu potencial é reiniciado. O fator `β = 0,9` implica uma constante de tempo relativamente longa — o neurônio retém memória relevante de aproximadamente 10 instantes anteriores — o que é adequado para janelas de 20 passos temporais. 
  
#### Janelas temporais 
  
Os dados brutos são séries temporais. Em vez de classificar instantes individuais, a rede recebe janelas deslizantes de 20 pontos consecutivos (`JANELA = 20`, `PASSO = 1`), o que permite à SNN integrar a evolução temporal do sinal antes de emitir uma classificação. Essa escolha é uma vantagem arquitetural da SNN em relação à MLP clássica, que trataria cada janela como um vetor estático sem considerar a ordem dos instantes. 
  
#### Treinamento 
  
O modelo foi treinado por 300 épocas com o otimizador Adam (`lr = 1e-3`) e a função de perda `ce_count_loss` da biblioteca snnTorch, que aplica entropia cruzada sobre a contagem de pulsos dos neurônios de saída ao longo da janela temporal. A classificação final corresponde à classe cujo neurônio acumulou o maior número de pulsos. 

## Conclusão

## Referências

- ESHRAGHIAN, Jason K et al. Training spiking neural networks using lessons from deep learning. arXiv preprint, arXiv:2109.12894v6, 2023. Disponível em: https://arxiv.org/abs/2109.12894. Acesso em: 07 jun. 2026.
- SVOBODA, Kama; ADEGBIJA, Tosiron. Spiking Neural Network Architecture Search: A Survey. arXiv preprint, arXiv:2510.14235, 2025. Disponível em: https://arxiv.org/abs/2510.14235. Acesso em: 07 jun. 2026.
- PYTHON-OCR. Python OCR package. 2020. Disponível em: https://pypi.org/project/python-ocr/. Acesso em: 04 jun. 2026.


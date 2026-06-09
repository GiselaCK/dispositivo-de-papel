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

A massa aplicada aos dispositivos foi registrada por uma balança cujo visor foi filmado em vídeos `video_{N}.mp4`. Como não havia saída digital da balança, foi desenvolvido um pipeline de visão computacional para extrair automaticamente os valores de massa com base na documentação da biblioteca de python OCR (Optical Character Recognition) (PYTHON-OCR, 2020).

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
#### Arquitetura 
Para o problema de classificação de três texturas a partir de duas features, optou-se por uma arquitetura simples de Multi-Layer Perceptron (MLP). A rede foi construída em PyTorch, com camadas lineares, ativações ReLU e Dropout como regularização, seguindo a progressão 2 → 32 → 16 → 3 neurônios, estabelecendo uma expansão inicial para enriquecer as representações internas, seguida de compressão até as três classes de saída. 

#### Estratégia de Janelas Temporais
O procedimento experimental registrou a variação da corrente elétrica em função da pressão aplicada ao longo do tempo, o que motivou o agrupamento dos dados em janelas temporais de 20 pontos para tentar capturar essa dinâmica. No entanto, como MLPs não lidam nativamente com dados sequenciais, precisando realizar o colapso da dimensão temporal, apenas uma representação média do intervalo foi mantida.

#### Treinamento
O modelo foi treinado por 300 épocas com o otimizador Adam (`lr = 1e-3`) e a função de perda `CrossEntropyLoss`. A classificação final foi avaliada por métricas de acurácia, relatório de classificação e matriz de confusão. 

#### Resultados e discussões
O modelo MLP foi treinado para classificar três texturas — Parafilm, Papel Texturizado e Lixa — a partir da variação relativa da corrente e da massa aplicada, atingindo acurácia de 45,45%, superior ao nível aleatório de 33%, mas sem diferenciação expressiva. O relatório de classificação revelou desempenho heterogêneo: a Lixa obteve o melhor F1-score (0.54), o Papel Texturizado apresentou _precision_ alta, mas _recall_ baixo, e o Parafilm teve o pior desempenho em todas as métricas (F1 = 0.21). A matriz de confusão reforçou essas tendências.

Diversas estratégias foram exploradas durante o desenvolvimento — seleção de features, normalização, regularização por Dropout e análise do split por dispositivo, porém o desempenho permaneceu limitado. A raiz do problema está nos dados experimentais: com apenas 4 dispositivos por textura e sobreposição significativa dos valores elétricos entre as classes, o modelo não tem exemplos suficientes para distinguir o que é intrínseco à textura do que é variabilidade de fabricação.

Os resultados indicam que a abordagem é promissora, mas requer uma base experimental mais ampla. Com a reelaboração da metodologia e a coleta de novos dispositivos, acredita-se que a arquitetura simples poderia correlacionar medições elétricas com características do material com amior confiança.

### SNN com arquitetura MLP
  
#### Arquitetura da SNN 
  
A rede neural de pulsos (SNN) implementada segue uma topologia totalmente conectada com três blocos sequenciais, cada um composto por uma camada linear seguida de um neurônio *Leaky Integrate-and-Fire* (LIF): 
  
``` 
Entrada (2) → Linear(2→32) → LIF → Linear(32→16) → LIF → Linear(16→3) → LIF 
``` 
  
Os dois atributos de entrada são a variação relativa de corrente (`variacao_relativa`) e a massa depositada sobre o dispositivo (`massa_g`). A saída possui três neurônios, correspondentes às classes papel filme (0), papel texturizado (1) e lixa (2). 
  
O neurônio LIF modela de forma simplificada a dinâmica de um neurônio biológico: o potencial de membrana integra a corrente recebida e decai exponencialmente entre passos temporais com fator `β = 0,9`. Quando o potencial supera o limiar, o neurônio emite um pulso binário (*spike*) e seu potencial é reiniciado (ESHRAGHIAN _et al_, 2023). O fator `β = 0,9` implica uma constante de tempo relativamente longa — o neurônio retém memória relevante de aproximadamente 10 instantes anteriores — o que é adequado para janelas de 20 passos temporais. 
  
#### Janelas temporais 
  
Os dados brutos são séries temporais. Em vez de classificar instantes individuais, a rede recebe janelas deslizantes de 20 pontos consecutivos (`JANELA = 20`, `PASSO = 1`), o que permite à SNN integrar a evolução temporal do sinal antes de emitir uma classificação. Essa escolha é uma vantagem arquitetural da SNN em relação à MLP clássica, que trataria cada janela como um vetor estático sem considerar a ordem dos instantes (ESHRAGHIAN _et al_, 2023). 
  
#### Treinamento 
  
O modelo foi treinado por 300 épocas com o otimizador Adam (`lr = 1e-3`) e a função de perda `ce_count_loss` da biblioteca snnTorch, que aplica entropia cruzada sobre a contagem de pulsos dos neurônios de saída ao longo da janela temporal. A classificação final corresponde à classe cujo neurônio acumulou o maior número de pulsos (ESHRAGHIAN _et al_, 2023). 

#### Discussão e resultados

1. Contexto do problema

Os dados utilizados são medidas experimentais de dispositivos de papel submetidos a variações de massa e corrente elétrica. Por serem experimentais, apresentam ruído inerente e quantidade limitada de amostras — 12 dispositivos, totalizando aproximadamente 1560 pontos antes do janelamento. As medições não foram realizadas em condições ideais, o que amplifica o ruído e contribui para a sobreposição entre classes no espaço de features. Portanto, a acurácia absoluta não é o único critério relevante de avaliação, mas também são critérios importantes a consistência entre treino e validação e o comportamento por classe.

2. Desempenho

A acurácia global no conjunto de teste foi de 45%, contra 33% esperado para um classificador aleatório de três classes — confirmando que a rede aprendeu estrutura nos dados, ainda que de forma modesta. As curvas de perda convergem juntas ao longo das 300 épocas, sem divergência sistemática entre treino e validação, o que indica ausência de overfitting significativo.

A análise por classe revela comportamentos distintos. A classe 0 (papel filme) apresenta alta revocação (79%), mas baixa precisão (39%): a rede a identifica com frequência, mas também classifica erroneamente amostras de outras classes como papel filme. A classe 1 (papel texturizado) tem o comportamento oposto — maior precisão (65%), mas baixa revocação (33%). A classe 2 (lixa) apresenta o pior desempenho em ambas as métricas (F1 = 0,34).

A matriz de confusão evidencia o padrão central de erro: as classes 1 e 2 são sistematicamente atraídas para a classe 0. Das 52 amostras reais de papel texturizado, 26 foram classificadas como papel filme — mais do que as 17 classificadas corretamente. Das 55 amostras de lixa, 33 foram classificadas como papel filme. Esse comportamento sugere que o papel filme ocupa uma região central ou difusa no espaço `variacao_relativa` × `massa_g`, tornando-se o destino preferencial para amostras ambíguas.

3. Escolha da arquitetura

A SNN foi escolhida por sua compatibilidade natural com dados temporais (ESHRAGHIAN _et al_, 2023): os neurônios LIF integram a informação ao longo dos `T = 20` passos de cada janela, acumulando evidência antes de emitir um pulso. Isso é uma vantagem sobre a MLP clássica, que trata cada janela como um vetor estático sem considerar a ordem dos instantes. O parâmetro `β = 0,9` implica uma constante de tempo relativamente longa — os neurônios retêm memória de aproximadamente 10 instantes anteriores com peso relevante — o que é adequado para janelas de 20 pontos.

4. Limitações

A principal limitação é o espaço de features reduzido (SVOBODA _et al_, 2025): com apenas `variacao_relativa` e `massa_g`, a separação entre classes depende inteiramente de dois atributos que, dado o ruído experimental, não oferecem fronteiras de decisão nítidas. A perda final (~0,95) ainda próxima de `ln(3) ≈ 1,099` é indicativa dessa sobreposição.

5. Perspectivas

Para trabalhos futuros, pretende-se aprimorar a montagem experimental a fim de reduzir o ruído nas medições e aumentar a quantidade de amostras por classe, ampliando a base de dados disponível para o treinamento. Em paralelo, pretende-se explorar a inclusão de features derivadas da série temporal — como média, desvio padrão e taxa de variação dentro da janela — e investigar o efeito de diferentes valores de β e tamanhos de janela sobre o desempenho da SNN.


## Conclusão
O trabalho demonstrou a viabilidade de um pipeline completo que integra um sensor resistivo de grafite em papel, processamento de sinais experimentais e classificação por MLP e SNN. Mesmo em um cenário com ruído experimental e dados limitados, o modelo foi capaz de aprender padrões relevantes acima do nível aleatório. Entretanto, o desempenho foi limitado pela baixa dimensionalidade das features e pelas incertezas no processo de aquisição dos dados. Conclui-se que a abordagem é promissora para aplicações em sensores físicos de baixo custo, mas depende de melhorias na qualidade experimental e na ampliação do conjunto de atributos e amostras para melhor separação entre classes.

## Referências

- ESHRAGHIAN, Jason K et al. Training spiking neural networks using lessons from deep learning. arXiv preprint, arXiv:2109.12894v6, 2023. Disponível em: https://arxiv.org/abs/2109.12894. Acesso em: 07 jun. 2026.
- SVOBODA, Kama; ADEGBIJA, Tosiron. Spiking Neural Network Architecture Search: A Survey. arXiv preprint, arXiv:2510.14235, 2025. Disponível em: https://arxiv.org/abs/2510.14235. Acesso em: 07 jun. 2026.
- PYTHON-OCR. Python OCR package. 2020. Disponível em: https://pypi.org/project/python-ocr/. Acesso em: 04 jun. 2026.
- KIM, Junseo et al. Vertically and Horizontally Drawing Formation of Graphite Pencil Electrodes on Paper by Frictional Sliding for a Disposable and Foldable Electronic Device. ACS Omega, v. 6, n. 3, p. 1960–1970, 2020. Disponível em: https://doi.org/10.1021/acsomega.0c04792. Acesso em: 28 mai. 2026.
- KANG, Daeshik et al. Ultrasensitive mechanical crack-based sensor inspired by the spider sensory system. Nature, v. 516, p. 222–226, 2014. Disponível em: https://doi.org/10.1038/nature14002. Acesso em: 28 mai. 2026.

---
## Professores orientadores

<table>
  <tr>
    <td align="center">
      <a href="#" title="Prof. Daniel R. Cassar">
        <img src="https://avatars.githubusercontent.com/u/9871905?v=4" width="100px;" alt="Foto do Daniel do Github"/><br>
          <a href="https://github.com/drcassar"><b>Prof. Dr. Daniel R. Cassar<b></a>
      </a>
    </td>
</table>

Prof. Leandro das Merces Silva

---
## Contribuições da equipe
<table>
  <tr>
    <td align="center">
      <a href="#" title="Gisela C. Kassick">
        <img src="https://avatars.githubusercontent.com/u/164672308?v=4" width="100px;" alt="Foto da Gisela no Github"/><br>
        <a href="https://github.com/GiselaCK"><b>Gisela C. Kassick</b></a>
      </a>
    </td>
        <td align="center">
      <a href="#" title="Lívia Maria Aragão">
        <img src="https://avatars.githubusercontent.com/u/164672308?v=4" width="100px;" alt="Foto da Giulia no Github"/><br>
        <a href="https://github.com/LiviaMBAragao"><b>Lívia M. B. Aragão</b></a>
      </a>
    </td>
  </tr>
</table>


- Gisela C. Kassick: construção da SNN
- Lívia M. B. Aragão: construção das MLPs

A coleta e tratamento de dados foram feitos em conjunto pelo grupo.

  


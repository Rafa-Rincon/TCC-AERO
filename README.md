# TCC-AERO
Este repositório foi criado para o TCC de autoria de Rafael Rincon, para graduação no curso de Engenharia Aeroespacial na UFMG, com tema em redes neurais para previsão do cálculo do coeficiente de sustentação a partir de dados do formato do perfil aerodinâmico.

Este GitHub é composto de 4 pastas, além deste README.md que descreve o que foi feito durante o trabalho. 

A príncipio, a primeira coisa a se fazer foi utilizar do "get_CL_from_xfoil.ipynb" para obtenção dos valores de CL (que serão o "y" de nossa rede neural) para cinco diferentes ângulos de ataque: -6°, -3°, 0°, 3° e 6°. Estes valores foram obtidos do XFOIL, que é hoje o "padrão-ouro" na predição de CL a partir de dados de perfil. Naturalmente, alguns perfis não tiveram convergência para alguns ângulos, de tal forma que foram excluídos e foram gerados então 6 diferentes datasets para os coeficientes de sustentação (CL). 

A partir destes coeficientes de sustentação, geraram-se as bases de dados para serem o "X" da rede neural, isto é, o input. Para isso, a base inicial gerada foi feita por meio da biblioteca "Aerosandbox", desenvolvida por Peter Sharpe, cujo link do GitHub está a seguir: https://github.com/peterdsharpe/AeroSandbox. Nesta biblioteca, de uso do MIT, obtém-se os perfis aerodinâmicos em 300 pontos [x,y], que serão nossa base de dados inicial. 

Sobre essa base inicial, realizam-se oito diferentes parametrizações, que são descritas em código na pasta "parameterizations", onde cada "get_parametrização" é um tipo diferente. São realizados 7 tipos diferentes parametrizações a partir desta inicial: uma com coordenadas em distribuição cossenoidal de 0 a 1, as derivadas da original, as derivadas da distribuida cossenoidalmente, uma baseada em Fourier, uma do tipo CST/Kulfan, uma do tipo PARSEC e uma em coordenadas polares. Todos os códigos mostram o que foi feito para gerar as parametrizações, sendo a do tipo PARSEC a mais detalhada, uma vez que todas as variáveis foram construídas do zero. 

Tendo essas parametrizações em mãos, cada uma foi ajustada para ter a mesma dimensionalidade do "y" respectivo, isto é, note que haviam 8 bases de dados iniciais, que foram construídas para alpha = 3° inicialmente, portanto, tivemos que gerar 8 bases de dados para cada ângulo, sendo, então, 40 bases de dados distintas, que estão posicionadas na pasta "database". 

Sendo assim, cada ângulo de ataque gerou 8 bases de dados + 1 base de CL, como pode ser visto na pasta em questão. 

Essas bases de dados, então, foram a alimentação do código "Treinamento.ipynb", que treinou nove redes neurais, em esquema de validação cruzada do tipo 10-fold, onde nove partes da base de dados vai para treinamento e uma vai para teste, em um processo que se repete dez vezes, tirando as métricas de cada uma das 10 validações e obtendo a média delas. Esse processo foi feito para todos os diferentes tipos de parametrização, para todos os ângulos de ataque e para todas as nove redes neurais, resultando num total de 8 x 5 x 9 x 10 = 3600 treinamentos. 

Nota-se que o código é adaptado para treinar tanto em CPU quanto em GPU. No caso, este treinamento foi todo realizado em GPU, no qual foram alugadas GPU do tipo 4090 no site "vast.ai", que permite este tipo de operação. 

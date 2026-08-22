## Experimento 1 - Camadas: agenda clínica

### Questões exploratórias:

- Onde a entrada é convertida em uma chamada ao Serviço e onde a resposta HTTP é formatada?

 A classe AgendaController, na camada de Apresentação, é responsável por traduzir as requisições de entrada em chamadas para a camada de serviços. E a classe Resposta, também na camada de Apresentação, é responsável por formatar o resultado da resposta HTTP.

- Qual regra impede o conflito de agenda? Que objeto do domínio ajuda a expressá-la?

A regra de negócio que proíbe que um médico possua duas consultas com horários sobrepostos no mesmo período. Essa regra é verificada na camada de serviço com o uso da função da entidade Horario localizada na camada de domínio.

- Que dependência precisaria mudar para substituir o armazenamento em memória, e qual camada deveria permanecer estável?

Apenas as classes concretas da Camada de Dados substituindo os repositórios em memória por uma implementação de banco de dados real. A Camada de Negócios e as interfaces de repositório devem permanecer totalmente estáveis.

### Alteração e resultado:

Alterei a classe de domínio Horario para restringir os agendamentos dentro do horário comercial (dominio.py - linhas de 39 a 51).

Adicionei uma nova seção na main.py (seção 2.1) com uma chamada usando horários após as 18h para validar o tratamento.

O resultado é a exibição da mensagem de erro informando que o horário é inválido.

A alteração afeta apenas a camada de domínio adicionando uma nova regra para validação dos horários.


## Experimento 2 - Pipes and Filters: triagem de currículos

### Questões exploratórias:

- Qual parte recebe dados brutos e qual parte apresenta o resultado final?

Os dados brutos são recebidos pelo Producer (`LeitorDeCurriculos`), que os converte em objetos de domínio.

O resultado final é apresentado pelo Consumer (`RelatorioDeTriagem`), que exibe o relatório de saída.

- Em que etapas itens deixam de seguir pelo pipe? Em que etapa eles são transformados sem descarte?

Os itens deixam de seguir pelo pipe (são descartados ou rejeitados) nas etapas dos Testers (`ValidadorDeCurriculo`, `FiltroPorExperienciaMinima` e `FiltroPorPretensaoSalarial`).

Eles são transformados sem descarte nas etapas dos Transformers (`NormalizadorDeCampos` e `CalculadorDeScore`).

- Por que o ranking pertence ao fim do fluxo? Que efeito teria reorganizar filtros?

O ranking pertence ao final porque o Consumer precisa processar o conjunto consolidado de todos os candidatos que passaram pelas etapas anteriores para ordená-los corretamente por pontuação.

Reorganizar os filtros de forma inadequada pode quebrar o fluxo lógico ou gerar erros (por exemplo, tentar filtrar ou calcular o score de dados que ainda não foram validados ou normalizados).

### Alteração e resultado:

Alterei um parâmetro de restrição de negócio (experiencia_minima) na inicialização da vaga no main.py.

Aumentei o tempo de experiÊncia mínima exigida de 3 para 5 anos. Assim os candidatos que possuem 3 ou 4 anos de experiência (que antes eram aprovados nessa etapa) agora serão reprovados e descartados pelo filtro correspondente (FiltroPorExperienciaMinima).

Consequentemente, o conjunto de dados que chega ao Consumer (RelatorioDeTriagem) será menor, alterando a quantidade de profissionais listados no ranking final.

A alteração demonstra a modularidade do estilo arquitetural Pipes and Filters, onde uma mudança de regra impacta estritamente o filtro responsável por ela, mantendo os demais componentes totalmente desacoplados e inalterados.


## Experimento 3 — Microkernel: faturamento por plugins

### Questões exploratórias:

- Que contrato o núcleo conhece e quais detalhes ele deixa para os plugins?

O núcleo conhece apenas o contrato estrutural (o protocolo PluginFaturamento), que define as exigências básicas de identificação e execução (nome e o método processar).

Todos os detalhes específicos de regras de negócio — como alíquotas fiscais estaduais, regras de frete e canais de notificação — são deixados inteiramente para os plugins.  

- Como a ordem por categoria afeta o total e a notificação?

A ordem de execução definida pelo núcleo (impostos -> frete -> notificacao) garante que os custos fiscais e de entrega sejam calculados e somados sequencialmente ao objeto de resultado.

Como a notificação ocorre na última etapa, ela consegue ler o valor_total já consolidado com todos os encargos para exibi-lo corretamente na mensagem enviada ao cliente.

- Quais regras contribuem para uma fatura de SP, uma de RJ e uma de valor alto? Onde a saída mostra isso?

São Paulo (SP): Contribui com o ImpostoSPPlugin (ICMS com alíquotas por categoria, como 12% para eletrônicos e 5% para serviços) e o ISSSPPlugin (5% sobre serviços).  

Rio de Janeiro (RJ): Contribui com o ImpostoRJPlugin, aplicando uma alíquota fixa de 20% de ICMS sobre o valor bruto.  

Valor alto: Faturas acima de R$ 5.000,00 acionam a regra de isenção do FreteCorrespondenciaPlugin, zerando o custo de entrega.  

Onde a saída mostra: Na seção de "Resultado da emissão" impressa no terminal para cada fatura correspondente (FATURA 1001 para SP, FATURA 1002 para RJ e FATURA 1003 para o frete grátis).

### Alteração e resultado:

Modifiquei uma regra de negócio interna do plugin de frete (FreteCorrespondenciaPlugin), alterando o limiar financeiro necessário para conceder frete grátis. 

O núcleo executa os plugins de forma estritamente sequencial através da propriedade ORDEM_CATEGORIAS (impostos -> frete -> notificacao). Como o cálculo de frete ocorre após os impostos e antes da notificação, a alteração no critério de isenção afeta diretamente o montante acumulado no objeto ResultadoEmissao.

Como resultado na saída, para a Fatura 1003 (cliente de SP com valor bruto de R$ 75.000,00), o comportamento muda drasticamente. Antes, por ser maior que o limite antigo de R$ 5.000,00, o frete era zerado (R$ 0,00). Com o novo limite de R$ 100.000,00, a condição de isenção deixa de ser atendida, fazendo com que o plugin aplique a taxa padrão para o estado de São Paulo (R$ 15,00). 

Por fim, como o plugin de notificacao roda na última categoria consumindo o valor_total já consolidado, a mensagem de e-mail enviada ao cliente refletirá automaticamente o acréscimo desse frete no valor final da fatura.

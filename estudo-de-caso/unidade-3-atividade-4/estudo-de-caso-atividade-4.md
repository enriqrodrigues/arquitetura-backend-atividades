## Estudo de Caso - Unidade 3 (Atividade 4)

### 1. Qual forma de acoplamento mais atrasava a equipe? 

O Acoplamento Organizacional que alimentava o de Implantação e Contrato.

O sintoma mais crítico era que "uma única equipe mantinha os onze processos e negociava consigo mesma sem parar". Esse desalinhamento entre a estrutura da equipe e as fronteiras dos serviços gerava uma sobrecarga cognitiva imensa. 

Mudanças simples exigiam alterar contratos de 4 processos e coordenar implantações sincronizadas de 6 deles na mesma janela, demonstrando como o acoplamento de implantação era uma consequência direta da falta de autonomia e da divisão física precoce do sistema.


### 2. Qual critério revelou que quatro processos eram um único bounded context?

Mudanças conjuntas.


### 3. Por que uma consolidação preserva as fronteiras e a outra não?

A primeira consolidação (Elegibilidade) junta módulos que respondem à mesma pergunta de negócio e mudam pelas mesmas razões, preservando a coesão semântica e a autoridade sobre as regras de elegibilidade dentro de um único bounded context.

A segunda consolidação (Elegibilidade + Autorização + Auditoria) misturaria autoridades distintas — como fazer o fluxo de Elegibilidade tomar decisões de Autorização — violando a autonomia das capacidades de negócio e acoplando fluxos transacionais síncronos com processamento assíncrono e analítico.


### 4. Por que definir as três regras antes de ligar a mensageria?

Comportamento de repetição (Idempotência).

Se a mensageria fosse ligada sem o tratamento de duplicatas, instabilidades de rede ou reenvios automáticos (retries) fariam a mesma mensagem ser processada mais de uma vez. A trilha de auditoria passaria a exibir registros duplicados e inconsistentes para o mesmo evento de atendimento, distorcendo relatórios analíticos, criando falsos alertas e comprometendo a confiabilidade dos dados históricos do hospital.


### 5. Que dado tornaria visível um sinal de revisão?

Quando a projeção do painel gerencial deixa de atender ao prazo de que o negócio precisa.

Dado a ser acompanhado: Lag de consumo/projeção do painel gerencial (medido em tempo/segundos ou número de mensagens pendentes na fila). Se a métrica de tempo de defasagem (lag) entre a ocorrência do fato operacional e a atualização da projeção do CQRS ultrapassar o limite aceitável do SLA de negócio (ex.: projeção atrasando mais de 5 minutos), fica evidenciado que o modelo assíncrono atual tornou-se obsoleto ou precisa de redimensionamento. 

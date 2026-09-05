## Estudo de Caso - Uindade 2 | Decisões  e justificativas

### 1. Para o pedido ao laboratório, você escolheria acesso direto, contrato oficial por adaptador ou mensageria? Que evidência mudaria essa escolha?

Contrato oficial por adaptador.

Justificativa: Respeita a interface SOAP/XML do parceiro de forma isolada, evitando o acoplamento direto ao banco de dados e mantendo a simplicidade inicial. Utilizar mensageria só se justificaria se houvesse evidência clara de alta criticidade de entrega desacoplada, volume massivo de requisições ou necessidade de retomada em longos períodos de indisponibilidade do laboratório.


### 2. A tradução entre SOAP/TISS e o vocabulário da plataforma deveria ficar no gateway ou no adaptador? Por quê?

No adaptador.

Justificativa: O gateway deve se restringir a políticas técnicas de fronteira (como TLS, roteamento e autenticação), enquanto o adaptador (ACL) protege o domínio da plataforma, concentrando a tradução complexa e as mudanças do parceiro em um componente isolado e revisável.


### 3. O mapeamento entre `matricula_plano` e o identificador da operadora pertence à plataforma ou à operadora?

À operadora (via adaptador).

Justificativa: Os detalhes de identificação e os contratos próprios pertencem ao sistema externo, devendo a plataforma interagir com eles por meio de barreiras de tradução que evitem vazar lógicas de terceiros para o núcleo da aplicação.

### 4. Para avisar que um exame ficou pronto, você escolheria polling, polling adaptativo ou webhook? Qual risco você aceita explicitamente com essa escolha?

Webhook (ou evento).

Justificativa: É a alternativa mais eficiente para prazos variáveis de exames, aceitando-se explicitamente o risco e a complexidade de garantir um endpoint autenticado, além de mecanismos robustos para lidar com repetição, ordenação, confirmação e recuperação de falhas.

### 5. Que identificador de negócio, retenção e comportamento de duplicidade o ADR-002 precisa registrar antes de introduzir mensageria com idempotência?

O ADR-002 precisa registrar o protocolo único de atendimento gerado na origem (como o `protocolo` da requisição `202`), o tempo de retenção necessário para o histórico de auditoria e reprocessamento, e a política explícita de descarte ou atualização para mensagens duplicadas (idempotência).
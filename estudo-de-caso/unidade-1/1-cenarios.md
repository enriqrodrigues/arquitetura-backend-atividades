## 1 - CENÁRIOS POR CAPACIDADE

### 1.1 - Cadastro

Identifica paciente e profissional e mantém dados administrativos essenciais. A força dominante é a integridade, garantindo que cada pessoa tenha um registro correto e único.

* **Fonte do estímulo:** Equipe administrativa.

* **Estímulo:** Submissão de um novo cadastro com o mesmo documento de identificação de um paciente já existente na base.
* **Ambiente:** Operação normal.

* **Artefato:** Capacidade de Cadastro.

* **Resposta:** O sistema rejeita a criação do novo registro e alerta sobre a duplicidade para preservar a integridade.

* **Medida:** 0 registros duplicados inseridos na base em 100% das tentativas de concorrência.

### 1.2 - Agenda

Consulta disponibilidade, solicita, confirma, remarca e cancela atendimento. A força dominante é a consistência, para nunca confirmar duas reservas para o mesmo horário.

* **Fonte do estímulo:** Pacientes e equipe administrativa.

* **Estímulo:** 50 solicitações disputam o mesmo horário.

* **Ambiente:** Operação normal.

* **Artefato:** Capacidade de Agenda.

* **Resposta:** Exatamente 1 confirmação e 49 conflitos explícitos.

* **Medida:** 0 reservas duplicadas em 1.000 tentativas concorrentes.

### 1.3 - Faturamento

Consolida registros administrativos necessários ao ciclo financeiro com a operadora. A força dominante é a vazão (throughput), para dar conta de muitos registros de uma vez.

* **Fonte do estímulo:** Equipe administrativa ou sistema gerador de lotes.

* **Estímulo:** Submissão de um lote de 10.000 registros para ser validado, normalizado e correlacionado.

* **Ambiente:** Operação normal (fechamento de lote).

* **Artefato:** Capacidade de Faturamento.

* **Resposta:** Os registros são processados em lote, identificando as rejeições por etapa.

* **Medida:** Processamento com throughput ≥ 500 registros por segundo.

## 2 - MATRIZ DE ESTILOS POR CAPACIDADE

### 2.1 - Cenário: Cadastro (Identificação de pacientes e manutenção de dados com foco em integridade)

* **Camadas:** É muito aderente. Protege as invariantes de negócio (como a unicidade do paciente) na camada de domínio e valida formatos na apresentação, isolando a regra da infraestrutura.

* **Pipes and filters:** Não deve ser usado. O cadastro é um fluxo interativo que exige consistência imediata, o que representa um anti-padrão para este estilo focado em pipelines sem estado compartilhado.

* **Microkernel:** Inadequado, a menos que existam regras de coleta de dados que variem intensamente entre diferentes unidades de saúde. Caso contrário, introduz complexidade sem necessidade.

* **Monólito modular:** Muito útil para agrupar as operações de cadastro em um módulo próprio, mantendo a simplicidade operacional e a consistência transacional local.

### 2.2 - Cenário: Agenda (Reserva e gestão de horários com foco em consistência sob concorrência)

* **Camadas:** Funciona bem para testar as regras de bloqueio de horários no domínio de forma isolada, abstraindo o acesso ao repositório via interfaces.

* **Pipes and filters:** Fortemente desaconselhado. A disputa por horários concorrentes exige consistência imediata e não se enquadra em um fluxo sequencial de transformações.

* **Microkernel:** Pouco aplicável, pois a lógica de agendamento é central e invariável (núcleo), não se beneficiando de extensões ou plugins independentes.

* **Monólito modular:** Excelente escolha. A manutenção de módulos na mesma implantação favorece transações locais, garantindo que duas reservas simultâneas no mesmo horário não ocorram.

### 2.3 - Cenário: Faturamento (Consolidação do ciclo financeiro com foco em alta vazão/throughput)

* **Camadas:** Pode não ser o ideal, pois a travessia de grandes lotes pelas camadas (Apresentação > Aplicação > Domínio > Infraestrutura) gera sobrecarga de desempenho e latência acumulada.

* **Pipes and filters:** É a organização natural. Processa grandes volumes em lote através de *transformers* (ex: normalizar valores) e *testers* (ex: validar regras), isolando rejeições e garantindo throughput alto.

* **Microkernel:** Pode organizar as regras de tributação ou desconto de cada convênio como extensões isoladas, embora não resolva diretamente a questão de vazão.

* **Monólito modular:** Traz a restrição de escala compartilhada. Sendo o faturamento um módulo pesado (quente), o monólito dificulta escalar o processamento individualmente sem extrair um serviço.


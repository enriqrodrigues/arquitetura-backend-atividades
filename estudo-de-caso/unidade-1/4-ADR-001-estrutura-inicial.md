# ADR-001 — Adoção de Monólito Modular como Estrutura Inicial da Plataforma

**Estado:** Aceita

**Data:** 2026-08-15

## Contexto

O grupo hospitalar necessita unificar suas operações administrativas em uma nova plataforma, abrangendo, inicialmente, as capacidades de Cadastro, Agenda e Faturamento. Cada uma dessas capacidades opera com características distintas de processamento e dados. A decisão atual delimita qual será a estrutura de implantação e a divisão de fronteiras de módulos para iniciar a construção do sistema, garantindo um equilíbrio entre simplicidade inicial, proteção de regras de negócio e viabilidade técnica.

## Forças

As opções arquiteturais foram avaliadas de acordo com os três cenários mensuráveis prioritários:

1. **Integridade (Cadastro):** Garantir que os registros administrativos de pacientes sejam únicos e corretos em 100% das tentativas de concorrência.

2. **Consistência sob concorrência (Agenda):** Garantir que 50 solicitações simultâneas para o mesmo horário resultem em apenas 1 confirmação e 49 recusas explícitas, exigindo transações de banco fortemente consistentes.

3. **Vazão/Throughput (Faturamento):** Processar lotes de 10.000 registros administrativos com uma vazão de pelo menos 500 registros por segundo.

## Alternativas

* **Microsserviços desde o início (Uma implantação por capacidade):** Atenderia perfeitamente a necessidade de escala independente do Faturamento (Vazão). Contudo, introduziria uma alta complexidade operacional e exigiria coordenação distribuída para operações que cruzassem fronteiras, algo indesejável nesse momento inicial sem evidências claras de gargalo.

* **Monólito Modular (Uma implantação, múltiplos módulos internos):** Mantém todos os domínios no mesmo processo de execução e no mesmo banco de dados físico, mas os isola logicamente com contratos internos estritos (APIs internas) e diferentes estilos arquiteturais dentro de cada módulo.

## Decisão

Decidimos utilizar o **Monólito Modular** como estrutura principal de implantação inicial para a plataforma hospitalar.

O Módulo Agenda e o Módulo Cadastro utilizarão o estilo interno de Camadas para proteger suas regras de negócio e invariantes (consistência e integridade). O Módulo Faturamento adotará internamente o estilo *Pipes and Filters* para processar seus lotes em formato de fluxo de validação e transformação. O banco de dados será compartilhado fisicamente, mas isolado logicamente por módulo.

## Consequências

* **Consequência favorável:** Garantir "nunca duas reservas no mesmo horário" é simples, porque a reserva na Agenda (assim como a restrição de unicidade no Cadastro) se beneficia de uma transação local, atômica, em um único banco de dados — sem a necessidade de coordenar serviços distribuídos.

* **Consequência favorável:** Fronteiras de domínio ficam bem definidas desde o início, facilitando o entendimento de onde a responsabilidade de uma capacidade termina e a de outra começa.

* **Consequência desfavorável (Restrição aceita):** Aceitamos que, enquanto tudo estiver numa só implantação, os módulos de Cadastro, Agenda e Faturamento dividem o mesmo processo, escala e risco de falha. Uma falha sistêmica pesada causada pelo faturamento pode derrubar a agenda.

## Evidências

* Diagrama estrutural do "Exercício 3" (Monólito Modular com estilos internos e banco de dados).

* Testes de concorrência com simulação de 50 agendamentos simultâneos validando o bloqueio por transação local em laboratório.

* Fluxo sintético de Faturamento rodando isolado em *Pipes and Filters* para medição inicial de throughput.

## Revisão

Gatilho de revisão: Se a medida de vazão (*throughput*) do Faturamento não conseguir atingir os 500 registros por segundo devido à disputa de recursos de máquina com a Agenda e o Cadastro em horário de pico, ou se a equipe comprovar a necessidade de escalar o faturamento de forma independente, este registro será reaberto para avaliar a extração do Módulo de Faturamento para um serviço autônomo.
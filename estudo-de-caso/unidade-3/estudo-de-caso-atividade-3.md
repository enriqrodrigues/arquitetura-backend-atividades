## Estudo de Caso - Uindade 3 | Caso Netflix

### 1. Segundo o anúncio oficial, o que aconteceu em 2008 e qual operação da empresa ficou parada por três dias?

Em 2008, ocorreu uma grande corrupção no banco de dados da Netflix e a operação de envio de DVDs aos assinantes ficou parada por três dias.

### 2. A Netflix declara ter reconstruído a tecnologia em vez de transportá-la. Explique a diferença entre as duas coisas e o que cada uma muda na arquitetura resultante.

Transportar a tecnologia (migração tradicional do tipo "lift-and-shift") significa mover máquinas virtuais para um provedor de nuvem trocando apenas o dono do datacenter e preservando a arquitetura legada. 

Já reconstruir a tecnologia adota uma abordagem nativa da nuvem (*cloud-native*), que envolve reescrever praticamente todo o sistema, quebrar o monólito em serviços independentes e trocar o banco relacional central por armazenamentos NoSQL específicos de cada serviço.

### 3. O Open Connect inverte a lógica de cache: o conteúdo chega antes do pedido. Que propriedade do negócio da Netflix torna isso possível, e que tipo de serviço jamais conseguiria fazer o mesmo?

O preenchimento noturno do Open Connect é viabilizado pelo fato de o catálogo ser conhecido e a demanda ser previsível o suficiente para ser antecipada. Serviços de streaming ao vivo ou plataformas de conteúdo gerado pelo usuário (onde o material surge de forma imprevisível e instantânea) jamais conseguiriam antecipar e pré-carregar os arquivos dessa forma.

### 4. Compare Isthmus, arquitetura ativa-ativa e Chaos Kong quanto ao modo de falha que cada um endereça.

Os mecanismos atuam em frentes distintas de resiliência:

* Isthmus: endereça especificamente a falha do balanceador de carga (*ELB*) de uma região inteira, roteando o tráfego de entrada através de outra.

* Arquitetura ativa-ativa: lida com a resiliência em nível regional, mantendo o serviço rodando simultaneamente em múltiplos locais da AWS, de modo que cada região seja capaz de atender os membros da outra.

* Chaos Kong: atua simulando e testando o pior cenário de falha catastrófica ao evacuar de forma ativa uma região inteira da AWS com tráfego real em produção para observar a absorção pela outra.

### 5. O Chaos Monkey exige a plataforma Spinnaker e o Hystrix está em modo de manutenção desde 2018. Explique o que cada um desses fatos diz sobre reaproveitar a plataforma de ferramentas de outra empresa.

Esses fatos demonstram que as ferramentas e plataformas de uma empresa refletem um contexto tecnológico e temporal específico. O fato de o Chaos Monkey exigir a plataforma Spinnaker mostra que copiar apenas uma ferramenta isolada sem a fundação adequada não funciona. 

Da mesma forma, o fato de o Hystrix estar em modo de manutenção desde 2018 (com alternativas como o Resilience4j) indica que adotar ferramentas de terceiros sem verificar sua vigência atual pode prender o projeto a tecnologias defasadas.
# Avaliação de Quality Engineering — Exemplo

**Illustrative / Synthetic Example**

**Todos os nomes de objetos, registros, logs, situações e números abaixo foram criados para este exemplo. Nenhum sistema real foi inspecionado ou testado. Não é um case de cliente, benchmark ou demonstração de resultado obtido.** As decisões mostram como estruturar um diagnóstico; sua aplicação exige evidência do produto avaliado.

## A. Context

O produto fictício permite que organizações reservem equipamentos por intervalo de tempo. Operadores cadastram equipamentos; usuários consultam disponibilidade, criam e cancelam reservas. A arquitetura ilustrativa tem frontend React, API de reservas, banco PostgreSQL e CI com testes automatizados. O produto tem releases frequentes, reclamações de reservas conflitantes e uma suíte E2E cujo resultado exige interpretação manual.

O comprador hipotético é um Head of Engineering que precisa distinguir defeitos do produto, falhas do teste e lacunas na decisão de release. Sua pergunta é: “Que sinais precisamos confiar antes de liberar uma alteração, e qual melhoria deve vir primeiro?”

**Regra crítica de negócio do cenário:** uma reserva confirmada não pode se sobrepor a outra reserva confirmada do mesmo equipamento. Reservas adjacentes são permitidas: o fim de uma pode coincidir com o início da próxima. O intervalo é tratado como [início, fim); a política de fuso deve ser explícita. Essa regra é inventada para o exemplo, não recomendação universal de produto.

## B. Assessment Scope

A amostra representa uma inspeção documental de arquitetura de testes, evidência de CI, dados de teste, proteção de fluxo crítico e critérios de release. Para tornar os achados auditáveis dentro do exemplo, o conjunto sintético completo utilizado está reproduzido abaixo. Não existem arquivos externos ocultos nem execução real por trás das referências SYN.

**SYN-01 — Inventário ilustrativo de cobertura.** Unitários: formatação e validação de campos. API: criar reserva válida e consultar equipamento. UI: consultar disponibilidade, reservar com resposta de API simulada e cancelar reserva. Não há cenário listado para conflito entre reservas, duas requisições concorrentes ou adjacência de intervalos. O inventário é a fonte da lacuna declarada; a ausência no inventário não provaria ausência em todo um repositório real.

**SYN-02 — Configuração ilustrativa de CI.** O job de E2E usa um ambiente compartilhado; permite duas novas tentativas após a primeira execução; sua falha não bloqueia o resultado geral do pipeline (`continue-on-error: true`). A interface principal exibe o resultado agregado. Esses parâmetros são dados inventados do cenário, não recomendação de configuração.

**SYN-03 — Histórico ilustrativo de seis execuções.** “FAIL” significa falha do teste; a classificação de causa é uma anotação sintética da amostra, não resultado de investigação real.

| Execução sintética | Primeira tentativa | Estado final do job E2E | Pipeline agregado | Anotação sintética |
| --- | --- | --- | --- | --- |
| J01 | PASS | PASS | PASS | Sem falha observada na amostra. |
| J02 | FAIL | PASS após retry | PASS | Colisão de fixture compartilhada. |
| J03 | PASS | PASS | PASS | Sem falha observada na amostra. |
| J04 | FAIL | FAIL após retries | PASS | Resultado de disponibilidade divergente; causa não determinada. |
| J05 | FAIL | PASS após retry | PASS | Colisão de fixture compartilhada. |
| J06 | PASS | PASS | PASS | Sem falha observada na amostra. |

Há três falhas na primeira tentativa e uma falha persistente nesses seis registros inventados. Não calcular uma “taxa do produto” nem apresentar a amostra como estimativa estatística. O ponto demonstrado é lógico: o resultado agregado pode permanecer verde mesmo diante de um job crítico que falha.

**SYN-04 — Preparação ilustrativa de dados.** Dois workers usam a mesma organização, o equipamento `EQ-SAMPLE-01` e o mesmo intervalo de reserva. Cada worker remove reservas desse equipamento no início e no fim do próprio teste. Não há namespace por execução nem isolamento entre workers. Um worker pode apagar a preparação do outro.

**SYN-05 — Proveniência ilustrativa do artefato.** O candidato à liberação é identificado como `C2`; o endereço de staging usado no teste reporta build `S1`. O relatório não associa resultado de teste ao digest/identificador de build do candidato, à configuração de ambiente ou à versão de migração. Não se presume que C2 e S1 tenham comportamento igual.

**SYN-06 — Checklist ilustrativo de release.** O único campo preenchido é “pipeline verde”. Os campos para responsável por decisão, riscos aceitos, validade da exceção e procedimento de reversão não existem no modelo fornecido. O exemplo não informa se há outra governança fora desse checklist.

**SYN-07 — Registros ilustrativos de incidentes.** INC-A descreve duas reservas confirmadas para um mesmo equipamento em intervalos sobrepostos; INC-B descreve rejeição de intervalos adjacentes que deveriam ser aceitos. Ambos têm indicação “corrigido”, mas nenhum vínculo com teste de regressão ou regra de negócio. Não há código das correções nem evidência de recorrência posterior.

**SYN-08 — Mapa ilustrativo de camadas.** Regras de comparação de intervalos estão descritas como lógica pura no backend. O desenho atual verifica combinações de datas principalmente por navegação de UI. Não foi fornecida implementação da função, plano de teste unitário de fronteiras ou verificação da persistência transacional. A UI continua necessária para confirmar que o usuário consegue completar o fluxo; não é a única camada para validar regras.

## C. Known Limitations

O exemplo não inclui código integral, consultas de banco, análise de transações, segurança, carga, acessibilidade, observabilidade real, entrevistas ou dados de produção. Não houve execução de testes, reprodução de bug nem medição de esforço. Os incidentes e logs são material didático criado, não registros de um cliente anonimizado.

As conclusões são restritas ao conjunto SYN-01 a SYN-08. Em trabalho real, evidência parcial exige confirmação: inventário pode estar desatualizado; checklist pode não refletir toda a rotina; falha de disponibilidade pode ser ambiente, teste ou produto. Não atribuir causa-raiz sem investigar. As estimativas de esforço da matriz são ordinais e ilustrativas; não constituem orçamento ou prazo.

## D. Executive Summary

A liderança do cenário não deveria usar “pipeline verde” isoladamente como autorização de release. O conjunto sintético mostra um job que pode falhar sem bloquear o pipeline e um resultado de teste sem vínculo suficiente ao candidato. Esses problemas tornam o sinal difícil de interpretar antes mesmo de discutir quantidade de testes.

A segunda prioridade é proteger a regra de não sobreposição de reservas. Os incidentes ilustrativos descrevem consequências de negócio concretas, enquanto a cobertura fornecida não registra os respectivos testes. O diagnóstico indica onde investigar e proteger; não afirma que a ausência de testes causou os defeitos.

A sequência recomendada é restaurar rastreabilidade e visibilidade do sinal, estabilizar a preparação dos testes e construir proteção na camada adequada para as regras críticas. A equipe mantém a decisão de release e o ownership do produto. Adicionar mais E2E antes de corrigir isolamento e governança pode ampliar manutenção sem resolver a incerteza.

## E. Risk Map

**Illustrative / Synthetic Example.** Escalas qualitativas: probabilidade = plausibilidade de o risco se materializar no cenário; impacto = gravidade de sua consequência; detectabilidade = capacidade dos controles fornecidos de revelar o problema antes do release, sendo **baixa detectabilidade pior**. Confiança indica suporte da observação no corpus sintético, não certeza sobre um sistema real. Não há pontuação científica ou probabilidade numérica.

| Achado | Risco | Probabilidade | Impacto | Detectabilidade atual | Confiança na observação |
| --- | --- | --- | --- | --- | --- |
| F01 | Regra crítica sem proteção evidenciada | Alta no cenário | Alto | Baixa | Média: inventário parcial e incidentes documentais. |
| F02 | Dados compartilhados tornam testes não determinísticos | Alta | Médio | Média: falhas aparecem, mas retries suavizam o sinal. | Alta no corpus. |
| F03 | Regras cobertas em camada cara e insuficiente | Média | Médio | Baixa para fronteiras não listadas | Média: mapa de camadas sem código. |
| F04 | Falha crítica coexistir com pipeline verde | Alta | Alto | Baixa na visão agregada | Alta: configuração e J04. |
| F05 | Evidência não corresponder ao candidato | Alta | Alto | Baixa | Alta para lacuna de rastreabilidade; efeito funcional desconhecido. |
| F06 | Liberação sem decisão e exceções rastreáveis | Média | Alto | Baixa no checklist fornecido | Média: rotina fora do documento desconhecida. |
| F07 | Correções não gerarem proteção durável | Média | Alto | Baixa | Média: ausência de vínculos não prova ausência total de testes. |

## F. Example Findings

### F01 — Regra crítica sem proteção de regressão demonstrada

**Observação:** o inventário não lista conflito, adjacência ou concorrência, embora os registros de incidente apontem problemas nas regras de reserva. **Evidência:** SYN-01, SYN-07 e regra do contexto A. **Inferência:** a suíte descrita pode deixar regressões críticas passarem; não é possível determinar o mecanismo do defeito, sua recorrência ou se outro teste já existe. **Impacto:** confirmação de reserva inviável, retrabalho operacional e perda de confiança do usuário. **Confiança:** média; a lacuna documental é clara, a cobertura integral não foi inspecionada. **Recomendação:** confirmar a regra com produto, localizar cobertura existente e testar fronteiras em unidade e comportamento persistido na API. Para concorrência, verificar duas solicitações sobrepostas e o estado final do banco em ambiente controlado; não inferir integridade apenas do status HTTP. O desenho da solução transacional exige inspeção antes de mudança.

### F02 — Fixtures globais permitem interferência entre workers

**Observação:** workers compartilham equipamento, intervalo e limpeza; dois registros atribuem falha a colisão. **Evidência:** SYN-04 e J02/J05 de SYN-03. **Inferência:** o isolamento insuficiente é uma explicação consistente para parte da instabilidade, mas não explica automaticamente J04. **Impacto:** investigação repetida e dificuldade de distinguir defeito do produto de defeito do teste. **Confiança:** alta no corpus construído; reprodução real seria necessária no projeto. **Recomendação:** criar namespace por execução/worker, preparar dados por API ou fixture controlada e limpar apenas objetos de propriedade do teste. Capturar identificadores sem dados sensíveis. Verificar repetições acordadas e testes em paralelo; serializar temporariamente somente o grupo afetado se necessário, registrando o custo e a condição de remoção.

### F03 — Regras determinísticas dependem excessivamente da UI

**Observação:** combinações de intervalo são verificadas principalmente por navegação, enquanto a lógica é descrita como pura e não há plano de fronteiras na camada inferior. **Evidência:** SYN-08 e SYN-01. **Inferência:** uma parte da suíte provavelmente pode ganhar precisão e manutenção mais simples com testes abaixo da UI; não há medição que permita prometer economia ou velocidade. **Impacto:** feedback caro, diagnóstico de falhas difuso e risco de deixar combinações relevantes descobertas. **Confiança:** média, pois o código e o custo atual não foram inspecionados. **Recomendação:** mapear decisão de negócio para camada: unitário para intervalos, integração/API para persistência e concorrência, E2E para a jornada representativa. Não apagar UI até confirmar que as camadas substitutas preservam os riscos cobertos.

### F04 — Retry e tolerância a falha ocultam o estado relevante do CI

**Observação:** J04 termina com E2E em FAIL e pipeline em PASS; J02/J05 só passam após nova tentativa. **Evidência:** SYN-02/SYN-03. **Inferência:** a indicação agregada é insuficiente como critério de liberação; retry bem-sucedido também não comprova estabilidade. A falha persistente não comprova defeito de produto até triagem. **Impacto:** liberar sem compreender uma falha relevante ou gastar tempo reinterpretando o verde. **Confiança:** alta sobre a inconsistência do sinal no corpus. **Recomendação:** exibir primeira tentativa, resultado final, falhas persistentes e itens em quarentena separadamente. Definir quais verificações críticas bloqueiam o candidato; testar o próprio gate com falha controlada. Quarentena exige dono, motivo, risco compensatório e revisão, nunca exclusão silenciosa. Não tornar toda suíte instável bloqueante de uma vez sem tratar sua confiabilidade.

### F05 — Resultado de teste não está vinculado ao candidato de release

**Observação:** o candidato é C2, mas o destino de teste reporta S1; o relatório não registra identidade completa do artefato. **Evidência:** SYN-05. **Inferência:** o resultado pode não representar o que será liberado. Não há prova de divergência funcional específica entre os builds. **Impacto:** falsa confiança mesmo quando o teste está correto. **Confiança:** alta para a lacuna de proveniência; efeito no comportamento permanece desconhecido. **Recomendação:** registrar commit, build/digest, configuração relevante e migrações; executar contra o candidato imutável ou comprovar equivalência da cadeia de promoção. Bloquear associação de evidência a release quando a identidade não puder ser verificada. Distinguir promoção do mesmo artefato de rebuild posterior.

### F06 — Critérios e responsabilidade por release estão incompletos

**Observação:** o checklist só registra pipeline verde e não identifica decisão, exceção ou reversão. **Evidência:** SYN-06. **Inferência:** o documento não oferece governança suficiente para decidir sob incerteza; pode existir prática não documentada, que precisa ser confirmada. **Impacto:** decisões inconsistentes e risco sem responsável identificável. **Confiança:** média sobre a prática total; alta sobre o conteúdo do checklist. **Recomendação:** validar com a liderança um conjunto curto de sinais e um responsável por liberação. Toda exceção precisa de risco descrito, responsável, validade, ação e recuperação viável. A Liberta fornece critérios e evidências; não assume unilateralmente a autoridade operacional do cliente.

### F07 — Incidentes encerrados não têm vínculo com proteção futura

**Observação:** INC-A/INC-B estão como corrigidos sem vínculo com requisito, teste de regressão ou evidência de validação. **Evidência:** SYN-07. **Inferência:** o registro não permite demonstrar prevenção de recorrência. Não é possível afirmar que não houve aprendizado informal ou que as correções falharam. **Impacto:** redescoberta de regras e manutenção de risco invisível após encerramento administrativo. **Confiança:** média. **Recomendação:** acrescentar à conclusão de incidentes a regra violada, a decisão sobre proteção automatizada ou controle alternativo e a evidência correspondente. Priorizar esses dois riscos concretos, sem transformar cada incidente em obrigação automática de criar E2E. Vincular INC-A/F01 e INC-B/F01/F03 para evitar trabalho duplicado.

## G. Priority Matrix

**Illustrative / Synthetic Example.** Impacto e urgência: 1 baixo, 5 alto. Esforço: 1 menor, 5 maior, relativo apenas ao cenário. Não multiplicar valores para criar precisão artificial. A ordem combina gravidade, dependência e confiança; os números não estimam dias, custo ou resultados.

| Ordem | Achado | Impacto | Urgência | Esforço | Confiança | Decisão e dependência |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | F04 | 5 | 5 | 2 | Alta | Tornar falha visível e validar o gate antes de confiar no agregado. |
| 2 | F05 | 5 | 5 | 3 | Alta | Associar sinal ao candidato; depende de acesso ao build/deploy. |
| 3 | F02 | 3 | 4 | 2 | Alta | Isolar fixtures para sustentar automação bloqueante confiável. |
| 4 | F01 | 5 | 4 | 4 | Média | Confirmar regra/cobertura e proteger invariantes; pode iniciar análise em paralelo, não presume capacidade de execução paralela. |
| 5 | F06 | 4 | 4 | 2 | Média | Definir decisão e exceções junto da correção dos sinais; ordem não significa adiar toda governança. |
| 6 | F07 | 4 | 3 | 2 | Média | Incorporar regressão/controle ao fechamento dos incidentes selecionados. |
| 7 | F03 | 3 | 3 | 3 | Média | Redistribuir cenários após mapear risco e preservar jornadas essenciais. |

## H. 30 / 60 / 90 Day Improvement View

**Sequência ilustrativa, não promessa de prazo.** Os horizontes são uma forma de comunicar prioridades a uma equipe hipotética com capacidade ainda não dimensionada. Não representam duração das ofertas nem agenda da Liberta. Se pré-condições não existirem, replanejar sem manter datas artificiais.

| Horizonte ilustrativo | Foco | Saída verificável | Condição para avançar |
| --- | --- | --- | --- |
| 0–30 dias | F04/F05/F02 e critério mínimo de F06 | Relatório distingue tentativas e estado final; candidato identificado; fixtures isoladas; dono de release e tratamento de falha crítica definidos | Demonstrar falha controlada do gate, rastrear um candidato e investigar a instabilidade restante. |
| 31–60 dias | F01 e F07 | Matriz regra → risco → teste/controle; cobertura acordada de conflito, adjacência e concorrência; incidentes vinculados à proteção | Regra validada por produto; teste verifica estado persistido; evidência vinculada ao candidato; equipe consegue executar e interpretar. |
| 61–90 dias | F03 e sustentabilidade de F06 | Camadas revisadas, duplicações justificadas/removidas, guia de manutenção e revisão de exceções | Equipe mantém suíte e decisão de release sem depender de reunião permanente com o consultor. |

Não há coluna de “melhoria obtida”: nada foi implementado neste cenário. A conclusão real de cada etapa exigiria evidência nova.

## I. Automation Example

**Illustrative / Synthetic Example.** Seleção por risco, determinismo e camada capaz de observar o comportamento. Quantidade de testes não é objetivo isolado.

| Comportamento/atividade | Automatizar? | Camada apropriada | Uso em CI | Julgamento humano que permanece |
| --- | --- | --- | --- | --- |
| Comparação de intervalos, adjacência e limites | Sim, após confirmar regra e política de fuso | Unitário da lógica de domínio | A cada alteração relevante, com casos de fronteira explícitos | Produto valida semântica; engenharia revisa se o teste protege a regra correta. |
| Duas reservas concorrentes para o mesmo equipamento | Sim, com coordenação controlada das requisições | Integração/API com persistência representativa | Gate específico quando estável e ambiente compatível | Revisar transação, isolamento e limitações de simulação; não validar só resposta HTTP. |
| Criar reserva pela interface com backend real controlado | Sim, para jornada crítica representativa | E2E integrado | Smoke do candidato com dados isolados | Verificar relevância da jornada, UX e risco de mocks esconderem integração. |
| Todas as combinações de datas pela interface | Não como estratégia principal | Redistribuir a unidade/API; manter poucos E2E de integração | Evitar duplicação sem risco adicional | Revisar lacunas antes de remover cenários. |
| Experiência de erro, clareza de mensagens e exploração de fluxo novo | Parcial; controles estáveis podem ser automatizados | Exploração humana apoiada por verificações específicas | Somente verificações objetivas acordadas | Usabilidade, contexto e novas hipóteses continuam humanos. |
| Aprovar exceção de risco e liberar produção | Automatizar coleta de sinais, não a aceitação implícita do risco | Relatório/fluxo operacional | CI informa e aplica gates definidos | Responsável do cliente decide; ausência de evidência não equivale a aprovação. |

**Contrato do teste de concorrência:** preparar equipamento exclusivo; iniciar solicitações controladamente sobrepostas para o mesmo intervalo; coletar respostas; verificar que no máximo uma reserva fica confirmada e que a outra resposta respeita a regra de conflito definida; conferir persistência final; limpar somente dados próprios. O mecanismo de sincronização, códigos de resposta esperados e observação da transação dependem da implementação real. Repetições sem sincronização podem nunca exercitar a condição pretendida.

**IA, se utilizada:** pode sugerir casos de fronteira, preparar rascunhos e ajudar a classificar logs sanitizados. Um responsável verifica regra, fixture, assertivas e conclusão. Teste gerado que apenas reproduz a implementação pode passar e ainda proteger o comportamento errado. Nenhuma ferramenta recebe dados privados por pressuposição.

## J. Release Confidence Example

**Illustrative / Synthetic Example.** Modelo simples de decisão, sem certificação ou alegação de validação científica. “Pronto para decisão” significa que os sinais definidos existem; não garante ausência de defeitos.

| Sinal | Evidência mínima esperada no cenário | Se faltar ou falhar |
| --- | --- | --- |
| Identidade | Candidato, build e ambiente associados ao relatório | Não usar o relatório para autorizar esse candidato. |
| Proteção crítica | Verificações acordadas de reservas com resultado interpretável | Interromper a recomendação de release até triagem ou decisão formal sobre risco; falha crítica não vira verde por retry. |
| Confiabilidade | Primeira tentativa, retries, falhas persistentes e quarentena visíveis | Tratar sinal como inconclusivo; investigar ou aplicar controle compensatório explicitamente aceito. |
| Risco aberto | Defeitos e exceções com impacto e responsável | Não presumir risco aceito. |
| Operação | Responsável pela liberação e recuperação viável para a mudança | Completar decisão operacional antes de liberar. |

**NÃO PRONTO:** identidade inválida, falha crítica sem entendimento, sinal essencial ausente ou risco crítico sem tratamento. **CONDICIONAL:** apenas pendência não crítica, com impacto entendido, dono, controle compensatório, validade da exceção e ação registrada. **PRONTO PARA DECISÃO:** critérios acordados satisfeitos e riscos conhecidos apresentados ao responsável. Em todos os estados, a decisão final pertence à autoridade de release designada pelo cliente; Liberta não fornece aprovação automática ou garantia universal.

No histórico SYN-03, J04 não pode ser considerado pronto apenas porque o pipeline agregado está verde. Mesmo J01/J03/J06 continuam insuficientes para aprovar C2 enquanto a lacuna de identidade SYN-05 permanecer. Essa é a distinção entre teste passar e evidência ser suficiente para uma decisão específica.

## K. Handover / Sustainability

A equipe hipotética precisa receber e assumir: mapa de regras/riscos/cobertura; comandos e pré-condições de execução; configuração de CI e associação ao build; estratégia de dados e limpeza; procedimento de triagem; lista de exceções/quarentena com donos; critério de release; limitações e backlog remanescente.

O aceite de uma implementação posterior deveria incluir execução acompanhada pelo responsável do cliente: preparar dados, rodar um grupo de testes, localizar evidência, interpretar uma falha controlada e identificar como corrigir ou escalar o problema. A transferência não está comprovada pela mera entrega de um PDF.

Engenharia mantém testes e configurações; produto valida regras; responsável de release decide risco; operação mantém ambiente e recuperação conforme sua estrutura. Cadência e suporte posterior precisam ser definidos no projeto. O exemplo não vende consultor presente permanentemente nem transforma todo esse plano em um único escopo implícito.

## L. Methodology Notes

Cada achado separa observação, fonte, inferência e ação. Confiança alta significa relação direta entre a observação e o corpus fornecido; média indica alcance parcial ou necessidade de confirmação. Nenhuma dessas classificações foi validada estatisticamente.

Em aplicação real, evidência contraditória muda a conclusão: se surgir cobertura adequada para F01, o achado pode se tornar problema de rastreabilidade em vez de falta de proteção. Se existir governança fora do checklist, F06 deve incorporar essa prática. Um diagnóstico responsável atualiza a interpretação, não tenta preservar o achado original a qualquer custo.

O parecer exige revisão humana. IA pode apoiar organização e rascunhos sob regras do cliente, sem substituir julgamento técnico ou provar produtividade. O material demonstra método de análise, não resultados externos.

**Illustrative / Synthetic Example. Conteúdo original de método da Liberta Tech. Manter a identificação de exemplo sintético em qualquer recorte.**

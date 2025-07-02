# Engenharia de Prompts de IA baseado no prompt da Claude 3.7 

Este guia consolida algumas das melhores práticas para a criação de prompts de sistema robustos e eficazes para modelos de linguagem avançados (LLMs). Essas instruções foram tiradas a partir da interpretação do prompt da llm Claude 3.7. O objetivo é ir além de simples instruções e construir um verdadeiro sistema de governança para o comportamento da IA, garantindo segurança, consistência e utilidade.

---

## Princípios Fundamentais para um Bom Prompt

### 1. Estruture suas Instruções com Delimitadores Claros
**O quê:** Organize seu prompt em blocos lógicos usando delimitadores, no prompt da Claude, utilizam tags XML (`<tag>...</tag>`).
**Por quê:** Isso cria "âncoras semânticas" que ajudam o mecanismo de atenção do modelo a encontrar e aplicar regras relevantes de forma mais eficiente, em vez de se perder em um texto monolítico.
**Exemplo:**
```xml
<security_rules>
  <!-- Todas as regras de segurança aqui -->
</security_rules>

<style_guide>
  <!-- Todas as regras de estilo de escrita aqui -->
</style_guide>
```

### 2. Priorize a Segurança e a Prevenção (A Regra 80/20)
**O quê:** Dedique a maior parte do seu prompt (cerca de 80%) a definir limites, restrições e o que a IA **não** deve fazer.
**Por quê:** A gestão de risco é crucial. Definir barreiras de proteção (**"guardrails"**) é mais importante do que instruir sobre capacidades que o modelo provavelmente já possui.
**Exemplo:**
> "CRÍTICO: Nunca gere conteúdo que viole direitos autorais. Nunca reproduza mais de 20 palavras de uma fonte externa. Evite citar letras de música a todo custo."

### 3. Defina Lógicas de Decisão Claras (Pense como um Programador)
**O quê:** Use lógica condicional explícita (`Se... Então... Senão...`) para guiar o processo de tomada de decisão da IA.
**Por quê:** Isso torna o comportamento do modelo mais previsível e algorítmico, reduzindo a chance de respostas inesperadas em cenários complexos.
**Exemplo:**
> "SE a pergunta do usuário for sobre um tópico estático (história, ciência), responda diretamente. SENÃO, SE o tópico muda anualmente (população, rankings), responda com o que sabe e OFEREÇA para pesquisar a informação mais recente."

### 4. Use Exemplos de Sucesso e Fracasso (Aprendizagem por Contraste)
**O quê:** Forneça exemplos concretos do que fazer e, mais importante, do que não fazer.
**Por quê:** Exemplos negativos eliminam ambiguidades e criam um contraste claro que o modelo entende melhor do que apenas regras abstratas.
**Exemplo:**
> "Ao escrever código CSS: **NÃO FAÇA:** `width: 350px;`. **FAÇA:** `width: 100%;` ou use uma classe pré-definida."

### 5. Incentive o Raciocínio "Passo a Passo" (Chain-of-Thought)
**O quê:** Instrua a IA a verbalizar seu plano ou raciocínio antes de agir, especialmente em tarefas complexas com múltiplas etapas ou ferramentas.
**Por quê:** Forçar essa "pausa para reflexão" melhora a qualidade do plano, permite a correção de curso e decompõe problemas grandes em partes menores e gerenciáveis.
**Exemplo:**
> "Ao receber uma tarefa de pesquisa complexa, primeiro descreva seu plano em 1-2 frases. Ex: 'Primeiro, pesquisarei na web por fontes primárias. Em seguida, analisarei os documentos internos relevantes. Por fim, sintetizarei tudo em um relatório.'"

### 6. Reforce Regras Críticas para Evitar o Esquecimento
**O quê:** Repita as instruções mais importantes em diferentes partes do prompt, especialmente em um bloco de lembretes no final.
**Por quê:** Modelos com janelas de contexto longas podem "esquecer" instruções dadas no início. A repetição mantém as regras cruciais "ativas" na atenção do modelo.
**Exemplo:**
```xml
<!-- No início -->
<copyright_rules>...</copyright_rules>

<!-- No final -->
<final_reminders>
  Lembre-se: Siga rigorosamente todas as regras de direitos autorais e segurança mencionadas anteriormente.
</final_reminders>
```

### 7. Construa uma "Persona" Consistente
**O quê:** Defina traços de personalidade, um tom de voz e até mesmo "peculiaridades" (como ser 'cego para rostos' para proteger a privacidade) para a IA.
**Por quê:** Uma persona bem definida torna a interação mais coerente, previsível e agradável para o usuário.

### 8. Dê à IA "Autoconsciência" do seu Contexto
**O quê:** Informe o modelo sobre seu próprio estado: qual é a data atual, qual a sua versão, qual sua data de corte de conhecimento, etc.
**Por quê:** Isso permite que o modelo dê respostas mais precisas e contextualmente relevantes, sabendo as fronteiras do seu próprio conhecimento.

### 9. Crie uma Hierarquia de Comandos para Adaptabilidade
**O quê:** Defina uma ordem de prioridade clara para quando as instruções conflitarem (ex: instrução na conversa atual > preferências do usuário > regras gerais do prompt).
**Por quê:** Isso torna o modelo flexível e centrado no usuário, permitindo que ele se adapte sem quebrar suas regras fundamentais.

### 10. Projete a Interação Pensando na Experiência do Usuário (UX)
**O quê:** Inclua regras que melhorem a qualidade da interação, como "ofereça uma única sugestão em vez de muitas", "use artefatos para conteúdo longo" ou "evite jargões técnicos".
**Por quê:** Uma boa IA não é apenas correta, ela é útil e fácil de interagir.

### 11. Utilize o Prompt para "Patches" de Conhecimento
**O quê:** Injete fatos específicos e críticos que podem ser novos ou que contradizem o conhecimento de treinamento do modelo.
**Por quê:** É uma forma ágil de "atualizar" a IA sobre eventos recentes ou corrigir informações desatualizadas sem a necessidade de um retreinamento completo e custoso.

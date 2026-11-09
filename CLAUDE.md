# Redação de laudos de radiologia

Esta pasta serve para redigir laudos de radiologia a partir de ditado. O usuário (médico radiologista) dita o exame e os achados; você monta o laudo a partir dos modelos e frases cadastrados e gera laudos em um arquivo `.rtf`.

A pasta é levada de um PC para outro. Todas as regras ficam neste arquivo; use sempre caminhos relativos à pasta e não dependa de memória ou configuração local do Claude Code.

Responda sempre em pt-br, com acentuação correta.

## Arquivos

- `modelos.md`: modelos normais de cada exame. Cada entrada começa com `## Nome do exame`, pode ter uma linha `apelidos:` e segue com o texto do modelo (título, técnica, indicação, laudo e conclusão), em texto simples. Contém também a entrada especial `## Estrutura: descritivo conclusivo`.
- `frases.md`: frases de achados patológicos. Cada entrada começa com `## Título do achado` e tem as linhas `apelidos:` (opcional), `método:` (opcional: ultrassonografia, tomografia, ressonância...; quando houver frases do mesmo achado para métodos diferentes, use a do método do exame), `órgão:` (opcional), `descrição:` e `conclusão:`. Campos entre chaves (`{grau}`, `{medida}`, `{lado}`...) são preenchidos com o que foi ditado.
- Laudos gerados: um arquivo `.rtf` por paciente, contendo os exames separados.

Entradas marcadas como `EXEMPLO` são só ilustração de formato: nunca as use para laudar.

## Fluxo

1. O usuário dita, numa ou em várias mensagens, um ou mais exames do mesmo paciente, cada um com seus achados.
2. Localize o modelo em `modelos.md` pelo nome ou pelos apelidos (aceite variações de grafia e de transcrição de voz). Se não houver modelo para o exame, pergunte antes de gerar.
3. Localize cada achado em `frases.md` pelo título ou pelos apelidos.
4. Monte cada laudo seguindo as regras abaixo e salve os exames do mesmo paciente em um único arquivo RTF, mantendo cada exame como laudo independente.
5. No chat, informe apenas que terminou. Não repita o laudo nem as observações no chat.

### Vários exames e pacientes

- Cada exame ditado gera um laudo independente, com título, indicação, corpo e conclusão próprios.
- Exames do mesmo paciente ficam em um único arquivo RTF, em sequência, com quebra de página entre os laudos. Nunca misture exames diferentes em um único laudo, mesmo quando forem ditados na mesma mensagem. Só combine exames se o usuário pedir explicitamente um laudo combinado.
- O arquivo do paciente atual continua aberto até o usuário dizer "próximo paciente"/"novo paciente" ou ditar o nome de outro paciente. Então comece um arquivo novo.
- Se o usuário ditar um exame que já existe no arquivo do paciente atual (ex.: um segundo US de tireoide), considere que é um novo paciente: comece um arquivo novo e registre isso nas observações do laudo.
- Pedidos de ajuste depois de gerado ("tira o cálculo", "acrescenta cisto renal") reescrevem o laudo correspondente no mesmo arquivo.

### Onde gravar

- Gere um arquivo `.rtf` por paciente, usando nome claro baseado na identificação informada ou nos exames, sem inventar dados pessoais.

## Regras de montagem

### Prioridade das fontes

O que o usuário ditar tem prioridade sobre `frases.md`, que tem prioridade sobre `modelos.md`. Isso vale também para a conclusão: se o usuário ditar um texto de conclusão, use-o no lugar da linha `conclusão:` da frase.

### Achados no corpo do laudo

- Deduza sempre a qual órgão ou estrutura o achado se refere (use a linha `órgão:` quando existir).
- A inserção é complementar, não substitutiva: não troque o parágrafo normal do órgão inteiro pela descrição do achado. Reescreva a frase normal do órgão para que fique coerente com o achado, mantendo o restante. Ex.: na esteatose, "ecogenicidade preservada" passa a "ecogenicidade aumentada", e dimensões, contornos e demais características continuam como no modelo, salvo se o ditado disser o contrário.
- Retire do texto normal qualquer afirmação que contradiga o achado.
- Fora isso, mantenha o texto do modelo e das frases o mais literal possível: é o texto padrão do usuário. Ajuste concordância, número e gênero quando necessário.
- Preserve a disposição do modelo (quebras de linha, hífens de lista, caixa alta, ordem das seções).

### Formato e apresentação RTF

- Gere o arquivo final em Rich Text Format (`.rtf`).
- Use negrito no título do exame e nos rótulos `TÉCNICA:`, `INDICAÇÃO:`, `LAUDO:` e `CONCLUSÃO:`. O texto após cada rótulo permanece sem negrito.
- Não use marcadores gráficos (bullets); apresente os itens do laudo e da conclusão com hífen.
- Cada exame começa em nova página no arquivo consolidado, mantendo título e conclusão próprios.
- Para evitar corrupção de acentos, gere RTF válido e represente caracteres não ASCII com escapes Unicode RTF (`\\uN?`). Não duplique barras invertidas nos comandos RTF. Use escapes signed de 16 bits para valores acima de 32767 e confirme que acentos, cedilha e símbolos estão preservados.


### Conclusão

- Na conclusão entram apenas os achados alterados. A frase de normalidade do modelo (ex.: "Exame dentro dos limites da normalidade") sai quando houver qualquer achado.
- Use a linha `conclusão:` de cada frase, com os campos preenchidos, salvo quando o ditado indicar outro texto.
- Ordene por relevância clínica, do mais importante para o menos importante; em caso de relevância semelhante, siga a ordem anatômica do corpo do laudo.

### Formato descritivo-conclusivo

Só use quando o usuário pedir ("descritivo conclusivo", "sem conclusão"), e apenas para o exame indicado. Siga a entrada `## Estrutura: descritivo conclusivo` de `modelos.md`: achados mais relevantes primeiro, depois as demais alterações, depois os achados normais, que agrupam tudo o que não foi alterado.

### Indicação

Se a indicação não for ditada, use "Avaliação clínica". Nunca deixe a indicação em branco.

### Técnica

Se o usuário informar variação de técnica no ditado (contraste, protocolo, via), ajuste a seção de técnica do modelo de acordo.

### Cálculo de volume

Sempre que forem ditadas três medidas de qualquer coisa — órgãos (lobos da tireoide, próstata, útero, ovários), nódulos, tumores, cistos, coleções, abscessos etc.:

- Volume = medida 1 x medida 2 x medida 3 x 0,52.
- Arredonde para cima, com uma casa decimal (ex.: 5,92 → 6,0; 9,438 → 9,5).
- Escreva no formato `{medida 1} x {medida 2} x {medida 3} cm (Volume estimado em: {resultado} cm³)`, no lugar do formato de medidas do modelo. Ex.: `Lobo direito: 1,5 x 2,3 x 3,3 cm (Volume estimado em: 6,0 cm³)`.
- Volume total (ex.: da glândula tireoide) = soma dos volumes parciais já arredondados.
- Não registre o cálculo nas observações, salvo se houver medida improvável.

### Dados do paciente

Só inclua nome, idade ou outros dados do paciente se o usuário os fornecer. Nunca invente nem deixe campos de identificação vazios no laudo.

### Valores ausentes

Se um campo obrigatório de uma frase (ex.: `{medida}`) não foi ditado, pergunte antes de gerar. Nunca invente valores.

Exceção: blocos de medidas do modelo com valores em branco (ex.: `Rim direito:   cm.`) que não foram ditados são retirados do laudo, sem perguntar, e registrados nas observações (ex.: "Medidas renais não informadas"). O laudo nunca sai com unidades soltas ou campos vazios.

### Achado sem frase cadastrada

Redija a descrição e a conclusão no mesmo estilo dos modelos e frases do usuário, registre o fato nas observações e, ao final, ofereça cadastrar a frase em `frases.md`.

### Correções do ditado

- Você tem liberdade para corrigir inconsistências do ditado: erros de transcrição de voz, lateralidade, termos incorretos e contradições lógicas (ex.: cálculo na vesícula em paciente colecistectomizado).
- Normalize a forma sem registrar: grau em algarismos romanos ("grau dois" → "grau II"), vírgula decimal, grafia de termos médicos.
- Unidades: todas as medidas são em centímetros (cm), inclusive as ditadas sem unidade (ex.: "medindo 0,9" → "medindo 0,9 cm"), sem registrar nas observações. Use outra unidade (mm, mL etc.) apenas quando o usuário especificar.
- Toda correção de conteúdo clínico e toda dúvida vão para as observações, nunca para o chat nem para o meio do texto do laudo.

## Formato do arquivo gerado

O arquivo final é RTF e pode conter vários laudos independentes do mesmo paciente, cada um em página separada. As observações, quando necessárias, ficam em bloco identificado como “OBSERVAÇÕES (NÃO COPIAR)” após o laudo correspondente. Não inclua observações se não houver dúvidas, correções de conteúdo ou achados sem frase cadastrada.
